# 🤖 Asistente-bot — Chatbot para agendar eventos en el calendario



## 📌 ¿Qué resuelve?
Un asistente personal que resuelve la necesidad puntual de agendar eventos al calendario 
si hay disponibilidad. Este flujo permite conectar cualquiera de los modelos de lenguaje.


## 🔁 Flujo breve
1) Trigger (chat público/webhook) → mensaje del usuario
2) AI Agent procesa con memoria de ventana (últimos 10 mensajes)
3) Una vez que procesa el requerimiento del usuario genera el correo en formato HTML
4) Por último le muestra la vista previa al usuario para confirmar el envio
    - ¿Quieres que envíe este correo a example@mail.com?
5) Si el usuario acepta se realiza el envio 


## ⚡ Partes "sheites" (lo clave)
- Agente con herramientas: usa Gmail tool 
- Memoria conversacional: BufferWindow de 10 mensajes mantiene coherencia en la charla
- System prompt diseñado: procese el mensaje y lo retorne en formato HTML
- Multiproveedor LLM: conecta Ollama (local), OpenAI, Gemini u OpenRouter 


## 📑 Arquitectura mínima
- Nodos:
    - Chat Trigger (Webhook público): entry point
    - AI Agent: orquesta LLM + tool + memoria
    - Ollama (chat model)
    - Memory (BufferWindow): guarda últimos 10 mensajes
    - Gmail Tool: envio de correo 



## 🚀 Uso rápido
1) Importa `Asistente-personal.json` en n8n y configura credenciales del LLM.
2) Configura la credencial del nodo de Gmail.
3) Chatea: "Envia un correo para example@mail.com para recordarle que el almacenamiento del servidor de desarrollo debe ser extendido con prioridad.".
4) Envio: darle el okey al chat para que envie el mensaje propuesto por email.