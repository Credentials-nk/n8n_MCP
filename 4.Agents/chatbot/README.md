# 🤖 Wiki-bot — Chatbot conversacional con Wikipedia

## 📌 ¿Qué resuelve?
Un asistente conversacional (Ana) que consulta Wikipedia en tiempo real y responde con información validada, recordando el contexto de la charla. Incluye UI web lista para usar con estilos personalizados.

## 🔁 Flujo breve
1) Trigger (chat público/webhook) → mensaje del usuario
2) AI Agent procesa con memoria de ventana (últimos 10 mensajes)
3) Si necesita datos, invoca tool de Wikipedia → busca/extrae artículo
4) LLM (Ollama/OpenAI/Gemini/OpenRouter) genera respuesta (≤2 párrafos + referencias)
5) Responde al usuario en la interfaz web

## ⚡ Partes "sheites" (lo clave)
- Agente con herramientas: usa Wikipedia tool para validar información en vivo (evita alucinaciones)
- Memoria conversacional: BufferWindow de 10 mensajes mantiene coherencia en la charla
- System prompt diseñado: límite de extensión (2 párrafos), tono amable, referencias obligatorias en formato link
- UI plug-and-play: @n8n/chat embebido en HTML + CSS custom (variables CSS para branding)
- Multiproveedor LLM: conecta Ollama (local), OpenAI, Gemini u OpenRouter sin cambiar lógica (desacople)

## 📑 Arquitectura mínima
- Nodos:
  - Chat Trigger (webhook público): entry point
  - AI Agent: orquesta LLM + tool + memoria
  - Wikipedia Tool: búsqueda/extracción automática
  - Memory (BufferWindow): guarda últimos 10 mensajes
  - LLM (Ollama activo; OpenAI/Gemini/OpenRouter opcionales): generación de respuestas
- Frontend:
  - index.html: carga @n8n/chat y apunta al webhook
  - style.css: tema pastel azul claro (personalizable vía variables CSS)

## 🚀 Uso rápido
1) Importa `Wiki-bot.json` en n8n y configura credenciales del LLM.
2) Abre `index.html` en navegador (ajusta `webhookUrl` si cambió el ID).
3) Chatea: "¿Qué es la inteligencia artificial?" → Ana consulta Wikipedia y responde con links.

## 🔒 Notas
- Modo público: cualquiera con la URL puede usar el chat (ajustar si necesitas auth).
- System prompt: modifica reglas en AI Agent → options → systemMessage.
- Cambiar LLM: conecta/desconecta el modelo deseado en el AI Agent (Ollama es el activo).
- Estilos: edita variables CSS en `style.css` (colores, fuentes, dimensiones).
