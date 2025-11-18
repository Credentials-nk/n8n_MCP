# 🚀 WorkFlows n8n

> **Colección de workflows de automatización para n8n**  
> _Simplifica procesos complejos con automatizaciones inteligentes usando IA, APIs y agentes autónomos_

---

## 📚 Módulos Disponibles

### 🎯 [1. Intro](./1.Intro/)
**Workflow básico de integración con APIs externas**

- 🐾 Procesamiento de datos Pokémon
- 📊 Integración Google Sheets + PokeAPI
- 📧 Notificaciones automáticas por Gmail

_Ideal para: Aprender conceptos básicos de n8n_

---

### 👥 [2. RRHH](./2.RRHH/)
**Automatización de procesos de Recursos Humanos**

- 📝 Solicitud de tiempo libre (vacaciones, días personales)
- 🏥 Solicitud de parte médico
- 📨 Webhooks para integración con APIs REST
- ✅ Validaciones automáticas y notificaciones

_Ideal para: Departamentos de RRHH que buscan digitalizar procesos_

---

### 🔍 [3. Scraping](./3.Scraping/)
**Extracción y enriquecimiento de datos web**

- 🌐 Web scraping de cursos con Firecrawl
- 🗺️ Prospección de negocios desde Google Maps (Apify + Firecrawl)
- 🤖 Enriquecimiento de datos con LLMs
- 📊 Exportación a Google Sheets

_Ideal para: Investigación de mercado, prospección comercial, análisis competitivo_

---

### 🤖 [4. Agents](./4.Agents/)
**Agentes de IA con LangChain y herramientas personalizadas**

#### 📂 Subcarpetas:
- **[chatbot](./4.Agents/chatbot/)**: Bot conversacional con Wikipedia (Ana)
- **[Asistente-Personal](./4.Agents/Asistente-Personal/)**: Gestión de calendarios y emails
- **[Tools](./4.Agents/Tools/)**: Ejemplo de herramientas personalizadas (Pokemon_finder)
- **[Agente tienda en linea](./4.Agents/Agente%20tienda%20en%20linea/)**: Atención al cliente e-commerce con autenticación

_Ideal para: Implementar asistentes inteligentes con memoria y herramientas_

---

### 🔐 [5. Auth](./5.Auth/)
**Métodos de autenticación para workflows**

- 🔑 Basic Auth
- 📋 Header Auth
- 🎫 JWT (JSON Web Tokens)
- 📖 Guía de decisión según caso de uso

_Ideal para: Proteger webhooks y APIs de n8n_

---

### 📚 [6. RAG](./6.RAG/)
**Retrieval Augmented Generation - Sistema RAG estándar**

- 📄 Ingestión de documentos desde Google Drive
- 🧠 Vector Store con PostgreSQL + pgvector
- 🔍 Detección de duplicados (file_id + md5_checksum)
- ⚖️ Agente legal especializado con citación de fuentes
- 💬 Chat conversacional con memoria

_Ideal para: Consultar documentos legales, técnicos o knowledge bases_

---

### 🎙️ [7. Agent-voice](./7.Agent-voice/)
**Backend para agente de voz con ElevenLabs**

- 🎤 Integración con [ElevenLabs](https://elevenlabs.io/app/agents/agents)
- 🛒 API REST para consultar productos y órdenes
- 🔐 Protección con Header Auth
- 🚀 Túnel con Cloudflare

_Ideal para: E-commerce con atención por voz, IVR inteligente_

---

### 💬 [8. Bots](./8.Bots/)
**Bots conversacionales multimodales con IA**

#### 📂 Subcarpetas:
- **[Telegram](./8.Bots/Telegram/)**: Bot con soporte de texto, imágenes y audios
- **[WhatsApp](./8.Bots/WhatsApp/)**: Bot Business API con respuestas por voz

**Características comunes:**
- 🧠 IA multimodal (texto, imágenes, audio)
- 🎨 Análisis de imágenes con Vision AI
- 🎤 Transcripción de audio con Whisper
- 🔊 Síntesis de voz con TTS (WhatsApp)
- 💾 Memoria contextual por usuario

_Ideal para: Atención al cliente, soporte técnico, asistentes personales_

---

## 🛠️ Tecnologías Utilizadas

- **n8n**: Plataforma de automatización visual
- **LangChain**: Framework para aplicaciones con LLMs
- **Ollama**: Modelos LLM locales (llama3.2, deepseek, gpt-oss)
- **OpenAI**: GPT-4O, Whisper, TTS
- **Google Gemini**: Modelos de chat
- **PostgreSQL + pgvector**: Vector database para RAG
- **Supabase**: Backend alternativo
- **Firecrawl / Apify**: Web scraping
- **ElevenLabs**: Agentes de voz
- **Telegram / WhatsApp Business API**: Bots conversacionales

---

## 🚀 Instalación

```bash
# Clonar repositorio
git clone https://github.com/AppSmaGob/workflows-n8n.git
cd workflows-n8n

# Levantar n8n con Docker (opcional)
docker-compose up -d
```

Cada carpeta contiene workflows `.json` que puedes importar directamente en n8n.

---

## 📖 Estructura del Proyecto

```
workflows-n8n/
├── 1.Intro/              # Workflows introductorios
├── 2.RRHH/               # Automatización RRHH
├── 3.Scraping/           # Web scraping y prospección
├── 4.Agents/             # Agentes de IA con LangChain
│   ├── chatbot/
│   ├── Asistente-Personal/
│   ├── Tools/
│   └── Agente tienda en linea/
├── 5.Auth/               # Métodos de autenticación
├── 6.RAG/                # Sistema RAG con vector store
├── 7.Agent-voice/        # Backend para ElevenLabs
└── 8.Bots/               # Bots conversacionales
    ├── Telegram/
    └── WhatsApp/
```

---

## 🤝 Contribuciones

¿Tienes un workflow interesante? ¡Compártelo!

1. Fork el repositorio
2. Crea una rama (`git checkout -b feature/nuevo-workflow`)
3. Commit tus cambios (`git commit -m 'Agrega nuevo workflow'`)
4. Push a la rama (`git push origin feature/nuevo-workflow`)
5. Abre un Pull Request

---

## 📝 Licencia

Este proyecto está bajo la licencia MIT - ver el archivo [LICENSE](LICENSE) para más detalles.

---

## 🔗 Enlaces Útiles

- [Documentación oficial de n8n](https://docs.n8n.io/)
- [LangChain Documentation](https://python.langchain.com/)
- [Ollama Models](https://ollama.com/library)
- [ElevenLabs Agents](https://elevenlabs.io/app/agents/agents)

---

