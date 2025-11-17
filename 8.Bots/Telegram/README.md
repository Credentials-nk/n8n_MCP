# 🤖 Bot de Telegram con IA

## 📌 ¿Qué resuelve?
Bot de Telegram que responde mensajes de texto e imágenes usando IA. Ayuda con preguntas de cultura general y análisis de imágenes mediante modelos LLM locales (Ollama).

---

## 🔁 Flujo breve

### 📝 **/start**
Trigger → Obtener sticker aleatorio de Giphy → Descargar → Enviar sticker de bienvenida

### 💬 **Text Message**
Trigger → Mostrar "escribiendo..." → Procesar con AI Agent → Responder con mensaje de texto

### 🖼️ **Image Message**
Trigger → Mostrar "respondiendo..." → Seleccionar foto de mayor resolución → Descargar → Analizar con IA Vision → Enviar respuesta

---

## ⚡ Partes "sheites"

### 🧠 **IA multimodal**
- **Texto**: Usa modelo `gpt-oss:120b-cloud` para respuestas conversacionales
- **Imágenes**: Usa modelo `llama3.2-vision:11b` para análisis visual
- **Memoria**: Mantiene contexto de los últimos 10 mensajes por chat

### 🎯 **Switch inteligente**
Detecta automáticamente el tipo de mensaje:
- Comando `/start` → Envía sticker animado
- Mensaje de texto → Respuesta conversacional
- Imagen → Análisis con Vision AI

### 👁️ **Análisis de imágenes**
- Selecciona la foto de mayor resolución automáticamente
- Usa el caption de la imagen como prompt (o pregunta "¿Qué hay en la imagen?")
- El análisis se pasa al agente principal que genera respuesta natural

### 💬 **Chat Actions**
Muestra indicadores visuales mientras procesa:
- "escribiendo..." para mensajes de texto
- "respondiendo..." para imágenes

### 🎨 **Integración con Giphy**
Stickers animados de bienvenida obtenidos dinámicamente desde Giphy API con tag "hola"

---

## 🏗️ Arquitectura

```
Usuario → Telegram
   ↓
[Bot Trigger] → Switch (tipo de mensaje)
   ├── /start → Giphy API → Sticker
   ├── Texto → Ollama Chat → Respuesta
   └── Imagen → Llama Vision → Ollama Chat → Respuesta
```

---

## 🔑 Características clave
- ✅ Respuestas cortas (1-2 líneas máximo)
- ✅ Memoria contextual por usuario (chat_id como session key)
- ✅ Análisis de imágenes con caption personalizado
- ✅ Reply automático al mensaje original
- ✅ Chat actions para mejor UX
