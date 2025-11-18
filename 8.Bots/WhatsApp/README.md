# 🤖 Bot de WhatsApp con IA

## 📌 ¿Qué resuelve?
Bot de WhatsApp que responde mensajes de texto, audios, imágenes y documentos usando IA. Ayuda con preguntas de conocimiento general, transcribe audios, analiza imágenes y puede responder con texto o voz mediante modelos LLM locales (Ollama) y en la nube.

---

## 🔁 Flujo breve

### 💬 **Text Message**
Trigger → Normalizar número de teléfono → Switch tipo mensaje → Extraer información → AI Agent → Enviar respuesta por texto

### 🖼️ **Image Message**
Trigger → Normalizar número → Switch → Descargar media → Obtener URL → Descargar imagen → Analizar con Vision AI → Extraer información → AI Agent → Enviar respuesta

### 📄 **Document Image Message**
Trigger → Normalizar número → Switch → Descargar documento → Analizar imagen del documento → AI Agent → Enviar respuesta

### 🎤 **Audio Message**
Trigger → Normalizar número → Switch → Descargar audio → Transcribir con OpenAI Whisper → AI Agent → Detectar tipo → Enviar respuesta (texto o audio)

---

## ⚡ Partes "sheites"

### 🧠 **IA multimodal**
- **Texto**: Usa modelo `llama3.2:3b` (Ollama) para respuestas conversacionales
- **Audio**: Transcripción con OpenAI Whisper y síntesis de voz con OpenAI TTS
- **Imágenes**: Usa modelo `llama3.2-vision:11b` (Ollama) para análisis visual
- **Documentos**: Procesa imágenes enviadas como documentos
- **Alternativas**: Soporta Google Gemini y GPT-4O (deshabilitados por defecto)
- **Memoria**: Mantiene contexto de los últimos 10 mensajes por usuario (`wa_id` como session key)

### 🎯 **Switch inteligente**
Detecta automáticamente el tipo de mensaje:
- Mensaje de texto → Respuesta conversacional
- Imagen → Análisis con Vision AI
- Documento con imagen → Análisis del documento
- Audio → Transcripción y respuesta (texto o voz)

### 📱 **Normalización de números argentinos**
Convierte números de WhatsApp al formato correcto de Argentina:
```javascript
// Convierte 549XXXXXXXXX → 54XXXX15XXXXXXX
const phoneNumber = from.startsWith('549') 
  ? '54' + from.substring(3, 6) + from.charAt(6) + '15' + from.substring(7)
  : from;
```

### 👁️ **Análisis de imágenes**
- Descarga la imagen desde WhatsApp Business API
- Usa el caption de la imagen como prompt (o "Describe la imagen" por defecto)
- El análisis se pasa al agente principal que genera respuesta natural en español

### 🔐 **WhatsApp Business API**
Integración completa con WhatsApp Business:
- Webhook trigger para recibir mensajes
- Descarga de medios mediante Media API
- Envío de respuestas con Phone Number ID

### 🌍 **Respuestas en español**
Configurado para responder siempre en castellano (Español - Latino) independientemente del idioma de entrada. Máximo 5 líneas por respuesta para mantener concisión.

### 🎤 **Respuesta adaptativa**
Si el mensaje recibido es un audio, el bot responde con **audio sintetizado** usando OpenAI TTS. Para otros tipos de mensaje, responde con texto.

---

## 🏗️ Arquitectura

```
Usuario → WhatsApp Business API
   ↓
[Bot Trigger] → Normalizar teléfono → Switch (tipo de mensaje)
   ├── Texto → Ollama Chat → Respuesta texto
   ├── Audio → Whisper (transcripción) → Ollama Chat → TTS → Respuesta audio
   ├── Imagen → Download Media → Llama Vision → Ollama Chat → Respuesta texto
   └── Documento → Download Media → Llama Vision → Ollama Chat → Respuesta texto
```

---

## 🔑 Características clave
- ✅ Memoria contextual por usuario (wa_id como session key)
- ✅ Análisis de imágenes y documentos con caption personalizado
- ✅ Transcripción de audios con OpenAI Whisper
- ✅ Respuesta por voz usando OpenAI TTS (formato opus)
- ✅ Normalización automática de números telefónicos argentinos
- ✅ Respuestas siempre en español (máximo 5 líneas)
- ✅ Múltiples modelos de IA disponibles (Ollama, Gemini, GPT-4O)
- ✅ Integración nativa con WhatsApp Business API
