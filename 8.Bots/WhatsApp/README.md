# 🤖 Bot de WhatsApp con IA

## 📌 ¿Qué resuelve?
Bot de WhatsApp que responde mensajes de texto e imágenes usando IA. Ayuda con preguntas de conocimiento general y análisis de imágenes mediante modelos LLM locales (Ollama) y en la nube.

---

## 🔁 Flujo breve

### 💬 **Text Message**
Trigger → Normalizar número de teléfono → Switch tipo mensaje → Extraer información → AI Agent → Enviar respuesta

### 🖼️ **Image Message**
Trigger → Normalizar número → Switch → Descargar media → Obtener URL → Descargar imagen → Analizar con Vision AI → Extraer información → AI Agent → Enviar respuesta

---

## ⚡ Partes "sheites"

### 🧠 **IA multimodal**
- **Texto**: Usa modelo `deepseek-v3.1:671b-cloud` (Ollama) para respuestas conversacionales
- **Imágenes**: Usa modelo `llama3.2-vision:11b` (Ollama) para análisis visual
- **Alternativas**: Soporta Google Gemini y GPT-4O (deshabilitados por defecto)
- **Memoria**: Mantiene contexto de los últimos 10 mensajes por usuario (`wa_id` como session key)

### 🎯 **Switch inteligente**
Detecta automáticamente el tipo de mensaje:
- Mensaje de texto → Respuesta conversacional
- Imagen → Análisis con Vision AI

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
Configurado para responder siempre en castellano (Español - Latino) independientemente del idioma de entrada

---

## 🏗️ Arquitectura

```
Usuario → WhatsApp Business API
   ↓
[Bot Trigger] → Normalizar teléfono → Switch (tipo de mensaje)
   ├── Texto → Ollama Chat → Respuesta
   └── Imagen → Download Media → Llama Vision → Ollama Chat → Respuesta
```

---

## 🔑 Características clave
- ✅ Memoria contextual por usuario (wa_id como session key)
- ✅ Análisis de imágenes con caption personalizado
- ✅ Normalización automática de números telefónicos argentinos
- ✅ Respuestas siempre en español
- ✅ Múltiples modelos de IA disponibles (Ollama, Gemini, GPT-4O)
- ✅ Integración nativa con WhatsApp Business API
