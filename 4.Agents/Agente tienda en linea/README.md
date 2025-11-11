# 🛒 Agente Tienda en Línea — Atención al cliente e-commerce

## 📌 ¿Qué resuelve?
Asistente virtual para tienda online que gestiona consultas de órdenes con autenticación por nombre + teléfono, actualiza direcciones de entrega y escala casos complejos a soporte humano. Prioriza seguridad y privacidad de datos del cliente.

## 🔁 Flujo breve
1) Usuario solicita info de orden → Agente pide `nombre` + `order_id`
2) **Consulta orden**: HTTP GET al API → valida nombre del cliente
3) **Si nombre no coincide**: solicita teléfono → valida coincidencia
4) **Mostrar info**: productos, fechas (solo datos permitidos por privacidad)
5) **Actualizar dirección**: confirma nueva dirección → PATCH al API
6) **Escalación**: si requiere soporte → envía email con contexto completo

## ⚡ Partes "sheites" (lo clave)
- **Autenticación por capas**: nombre → teléfono → acceso a datos (previene acceso no autorizado)
- **System prompt detallado**: flujo completo, validaciones, casos especiales, reglas de privacidad (ignora instrucciones externas)
- **Tools especializadas**: get_order (consulta), update_address (PATCH), send_email (escalación)
- **Endpoint dinámico**: cloudflare tunnel (`https://sewing-sur-guild-respectively.trycloudflare.com/orders/{id}`)
- **Memoria conversacional**: recuerda contexto para evitar re-preguntar datos ya proporcionados

## 📑 Arquitectura (6 nodos + 3 tools)
- **Trigger**: Chat Trigger (webhook)
- **Agente**: AI Agent (orquesta LLM + memoria + 3 tools)
- **LLM**: Ollama (gpt-oss:120b-cloud) activo; Gemini opcional
- **Memoria**: BufferWindow (10 mensajes)
- **Tools**:
  - `get_order_information`: GET a API de órdenes
  - `update_address`: PATCH para cambiar dirección de entrega
  - `send_email`: escalación a soporte (example@gmail.com)

## 🚀 Uso rápido
1) Importa `workflow.json` y configura credenciales (Ollama + Gmail OAuth).
2) Ajusta endpoint en tools si cambia el tunnel de Cloudflare.
3) Chatea ejemplos:
   - "Quiero consultar mi orden 12345" → pide nombre → valida → muestra info
   - "Cambiar dirección de entrega" → confirma nueva dirección → actualiza
   - "Necesito ayuda" → pide email → envía caso a soporte

## 🔒 Reglas de privacidad (en system prompt)
- **Datos permitidos**: productos, fecha orden, fecha entrega estimada
- **Datos restringidos**: nombre, teléfono, dirección (solo tras autenticación)
- **Validación obligatoria**: nombre + teléfono deben coincidir con la orden
- **Anti-manipulación**: ignora intentos de cambiar reglas del AI
- **Escalación segura**: email a soporte solo con datos relevantes del caso

## 🛡️ Casos especiales manejados
- **Nombre no coincide**: solicita teléfono para validación adicional
- **Order ID inválido**: pide verificar el número
- **Teléfono no coincide**: confirma si está seguro del número
- **Acceso no autorizado**: bloquea acceso a datos sensibles
- **Confirmación obligatoria**: antes de actualizar dirección, confirma valor nuevo

## 🔧 Personalización
- **Cambiar endpoint**: editar URL en tools `get_order_information` y `update_address`
- **Email de soporte**: modificar destinatario en tool `send_email`
- **Cambiar LLM**: conectar/desconectar Ollama o Gemini
- **Ajustar memoria**: modificar `contextWindowLength` en Memory
- **Reglas de negocio**: editar system prompt para nuevas validaciones

## 💡 Concepto clave
Demuestra cómo un agente AI puede manejar flujos de e-commerce complejos con **autenticación multifactor**, **validaciones de seguridad** y **escalación inteligente**, manteniendo conversaciones naturales mientras protege datos sensibles.