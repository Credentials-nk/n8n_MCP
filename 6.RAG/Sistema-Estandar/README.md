# 📚 RAG Sistema Estándar

## 📌 ¿Qué resuelve?
Sistema de Retrieval Augmented Generation (RAG) que permite consultar documentos legales mediante un asistente de IA. El sistema indexa PDFs desde Google Drive y responde consultas usando el contenido vectorizado.

---

## 🔁 Flujo del sistema

### 🔵 **Cadena 1: Alimentar BD (Ingestión)**
1. **Trigger** → Schedule o Manual que busca archivos en carpeta de Google Drive  
2. **Buscar archivos** → Obtiene lista de documentos en carpeta "Documentos Legales"  
3. **¿Es un archivo?** → Filtra carpetas, procesa solo archivos  
4. **Obtener carga previa** → Consulta tabla `ingested_files` para verificar si el archivo ya existe (compara `file_id` + `md5_checksum`)  
5. **¿Fue previamente cargado?**  
   - Si **ya existe** → Sale del workflow (evita duplicados)  
   - Si **no existe** → Continúa procesamiento  
6. **Borrar referencias** → Elimina vectores anteriores del mismo archivo (por si hubo modificaciones)  
7. **Insertar documento** → Registra archivo en tabla `ingested_files`  
8. **Descargar Archivo** → Obtiene el PDF desde Google Drive  
9. **Text Splitter** → Divide el documento en chunks con overlap de 250 caracteres  
10. **Default Data Loader** → Carga los chunks en formato binario  
11. **PGVector Store** → Genera embeddings (usando Ollama) y almacena vectores en PostgreSQL  
12. **Actualizar file_id** → Asocia los vectores con el `file_id` del documento  

### 🟢 **Cadena 2: Consumir BD (Consulta)**
1. **Chat message** → Usuario hace pregunta  
2. **AI Agent** → Recibe consulta y activa herramientas  
3. **Answer questions with vector store** → Busca en PGVector Store los chunks más relevantes  
4. **PGVector Store QS** → Recupera vectores relacionados  
5. **Ollama Chat** → Genera respuesta usando el contexto recuperado  
6. **Memory** → Mantiene historial de conversación (últimos 10 mensajes)  

---

## ⚡ Partes "clave"

### 🛡️ **Detección de archivos duplicados**
El sistema evita reprocesar archivos usando una **doble verificación**:

```sql
SELECT count(*) FROM ingested_files 
WHERE file_id = '{{ $json.id }}' 
  AND md5_checksum = '{{ $json.md5Checksum }}'
```

- **file_id**: Identifica el archivo de Google Drive  
- **md5_checksum**: Detecta si el contenido cambió  

Si el archivo ya está ingestado → **Sale del workflow**  
Si el archivo fue modificado (diferente MD5) → **Borra vectores viejos y reingesta**

### 🧠 **Vector Store con PostgreSQL + pgvector**
Usa PGVector en vez de Supabase o memoria RAM, lo que permite:
- Persistencia de datos  
- Escalabilidad  
- Consultas eficientes con embeddings de Ollama (`mxbai-embed-large`)  

### 🔧 **Text Splitter inteligente**
- Divide documentos en chunks  
- **Overlap de 250 caracteres** → Evita perder contexto entre chunks  
- Mejora la recuperación semántica  

### 🤖 **Agente Legal especializado**
Configurado como asistente legal que:
- Solo responde basado en los documentos disponibles  
- Cita fuente (nombre del documento y cláusula)  
- Si no encuentra información → Responde "No puedo encontrar la respuesta en los recursos disponibles"  

---

## 🏗️ Arquitectura

```
Google Drive (PDFs) 
   ↓
[Ingestión] → Text Splitter → Embeddings (Ollama) → PostgreSQL (pgvector)
   
[Consulta] → Chat → AI Agent → Vector Store → LLM (Ollama) → Respuesta
```

---

## 🔐 Seguridad
- Los documentos solo se cargan desde carpeta específica de Google Drive  
- No se expone información fuera de los documentos indexados  
- El agente está configurado para mantener confidencialidad  
