# 🗓️ Asistente Personal — Gestión inteligente de calendario y correos

## 📌 ¿Qué resuelve?
Asistente AI que administra calendario (Google Calendar) y correos (Gmail) con confirmación previa. Crea/consulta eventos respetando horario laboral, lee/redacta/envía emails en HTML con borradores de previsualización, y clasifica por prioridad. Recuerda contexto conversacional.

## 🔁 Flujo breve
1) Trigger (chat/webhook) → mensaje del usuario
2) AI Agent procesa con memoria (10 mensajes) y decide qué tool usar
3) **Calendario**: consulta disponibilidad → crea/modifica eventos (8am-4pm, L-V, sin traslapes)
4) **Correo**: lee emails → crea borrador HTML → muestra preview → confirma → envía
5) Responde con resumen de la acción realizada

## ⚡ Partes "sheites" (lo clave)
- Agente multi-tool: 5 herramientas (2 Calendar + 3 Gmail) orquestadas por el LLM según necesidad
- System prompt avanzado: reglas de horario laboral, anti-traslape, confirmación obligatoria, clasificación de correos, fecha dinámica ($now)
- Flujo de confirmación: crea borradores HTML antes de enviar (previene errores)
- Memoria conversacional: BufferWindow mantiene coherencia en solicitudes multi-paso
- Multiproveedor LLM: Ollama (activo) o Gemini sin cambiar lógica

## 📑 Arquitectura (6 nodos + tools)
- **Trigger**: Chat Trigger (webhook)
- **Agente**: AI Agent (orquesta LLM + memoria + tools)
- **LLM**: Ollama (gpt-oss) activo; Gemini opcional
- **Memoria**: BufferWindow (10 mensajes)
- **Tools Calendario**:
  - Obtener eventos (consultar disponibilidad)
  - Crear eventos (con validaciones horario/traslapes)
- **Tools Gmail**:
  - Obtener correos (lee últimos 2 por defecto)
  - Crear borrador (HTML)
  - Enviar correo (tras confirmación)

## 🚀 Uso rápido
1) Importa `workflow.json` y configura credenciales (Ollama/Gemini + Gmail OAuth + Google Calendar).
2) Chatea ejemplos:
   - **Calendario**: "Agenda reunión con equipo mañana a las 10am por 1 hora"
   - **Correo**: "Envía email a juan@ejemplo.com recordando fecha límite del proyecto"
3) El agente confirmará antes de crear eventos o enviar correos.

## 🔒 Reglas de negocio (en system prompt)
- **Horario**: solo 8am-4pm, Lunes a Viernes
- **Anti-traslape**: verifica disponibilidad antes de agendar
- **Confirmación**: siempre pide OK antes de crear/modificar/eliminar/enviar
- **Correos**: borrador HTML primero, firma "Sistemas - Bot"
- **Clasificación**: prioridad Alta/Media/Baja; ignora spam/promos
- **Recordatorios**: 15min antes de eventos
- **Resúmenes**: eventos diarios al inicio del día, semanales los lunes

## 🔧 Personalización
- Cambiar horario laboral: editar system prompt → "Horario laboral: X a Y"
- Cambiar calendario: en tool "Crear eventos" → seleccionar otro calendar
- Cambiar LLM: conectar/desconectar Ollama o Gemini
- Ajustar memoria: modificar `contextWindowLength` en Memory node
- Límite de correos: en "Obtener los correos" → ajustar `limit`

