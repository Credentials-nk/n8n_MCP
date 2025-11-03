# 🕷️ Web-scraping — Extracción y estandarización de cursos

## 📌 ¿Qué resuelve?
Este workflow automatiza la investigación de sitios de cursos (p. ej., páginas de instructor) para:
- Visitar cada sitio desde una Google Sheet
- Convertir el HTML a Markdown y extraer datos con un LLM (curso, instructor, URLs, descripción, categoría)
- Normalizar el resultado a un arreglo de cursos
- Registrar/actualizar la información en una pestaña de resultados en Google Sheets
- Enriquecer cada curso: scrapear la página de Udemy y generar un Google Doc con el temario procesado

En resumen: transforma páginas web heterogéneas en datos limpios y accionables, enlazados a documentos y una planilla.

---

## 🧭 Flujo (alto nivel)
1) Trigger (manual o schedule) 
2) Leer sitios desde Google Sheets 
3) Filtrar filas válidas 
4) HTTP Request (HTML) 
5) HTML → Markdown 
6) LLM (extracción estructurada) 
7) Fan-out de cursos 
8) Append/Update en Google Sheets 
9) Scrape Udemy (Firecrawl) 
10) Crear Google Doc del curso 
11) Escribir URL del Doc en la Sheet 
12) LLM (resumen/temario) 
13) Insertar contenido en el Doc.

---

## 🧩 Nodos principales y propósito
- Schedule/Manual Trigger: Ejecuta por cron o a demanda.
- Google Sheets (Get - sitios web): Fuente de URLs a investigar (hoja "Sitios Web a buscar").
- Filter: Asegura que exista `row_number` y `Sitio Web`.
- HTTP Request: Descarga el HTML de cada sitio.
- Markdown: Pasa HTML a Markdown para facilitar el parsing semántico con LLM.
- OpenAI (Message a model): Extrae cursos en JSON desde el Markdown y el URL base (jsonOutput=true).
- Split Out + Split In Batches: Fan-out por curso y procesamiento batched.
- Google Sheets (Resultados): Append/Update por "Nombre del curso" (idempotente).
- Firecrawl (nodo o API): Scrapea la página de Udemy para extraer contenido enriquecido (markdown, metadata).
- Google Docs (crear/actualizar): Crea un Doc con título saneado y luego inserta el contenido procesado.
- OpenAI (Extraer informacion del curso): Resume/transforma el markdown de Udemy a texto limpio apto para Google Docs.

---

## ⚡ Partes “sheites” (lo clave para el valor conceptual)
1) HTML → Markdown → LLM para extracción estructurada
   - El paso de Markdown reduce el ruido del HTML y hace más estable la extracción con LLM.
   - El prompt guía al modelo para devolver exactamente el JSON requerido.

2) Fan-out y procesamiento idempotente en Sheets
   - `SplitOut` + `SplitInBatches` permiten escalar con muchos cursos.
   - `appendOrUpdate` con columna de match ("Nombre del curso") evita duplicados y facilita re-ejecuciones.

3) Enriquecimiento con Firecrawl + pipeline de documentos
   - Scraping de la URL de Udemy para obtener contenido rico en markdown.
   - Creación automática de un Google Doc por curso y escritura del temario procesado.
   - Título saneado (`replaceAll(':', '').replaceAll('|', '-')`) para evitar errores en Docs.

4) Prompts con contexto del sitio y normalización del output
   - Se pasa la URL del sitio y el markdown al LLM con un formato de salida estrictamente JSON.
   - Segundo LLM convierte el markdown en texto listo para Docs (estructura legible para humanos).

5) Rutas alternativas y desacople de proveedores
   - Nodo nativo de Firecrawl y alternativa por API HTTP (útil si cambia el nodo o para features avanzadas).
   - Integración opcional con OpenRouter (chatTrigger + LLM chain) para experimentar sin afectar el flujo base.

---

## 📑 Datos de entrada/salida (contrato mínimo)
Entrada
- Google Sheet "Sitios Web a buscar" con columna: `Sitio Web` (y `row_number`).

Salida
- Google Sheet "Resultados":
  - Nombre del curso, Instructor, DevTalles, Udemy, Descripción, Google Docs - Temario.
- Google Docs: 1 documento por curso con el temario/descripcion procesada.

Errores comunes
- URL vacía o sitio inaccesible → el filtro evita filas incompletas; HTTP Request puede fallar si el site bloquea bots.
- JSON del LLM inválido → revisar prompt/temperatura y habilitar `jsonOutput` (ya activado).
- Campos Sheet no alineados → nombres de columnas deben coincidir (p. ej., "Udemy", "DevTalles").

---

## 🔐 Credenciales e integraciones
- Google Sheets/Docs: OAuth2 configurado (IDs de documento y carpeta en nodos).
- OpenAI / OpenRouter: Claves/API configuradas en credenciales.
- Firecrawl: Usar clave API segura en credenciales; evitar exponer tokens en cuerpos de requests.

Recomendaciones
- Mover cualquier token en texto plano a una credencial segura en n8n.
- Establecer límites de rate limit si el origen lo requiere.

---
