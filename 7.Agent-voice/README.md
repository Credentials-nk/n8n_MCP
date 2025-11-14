# 🎤 Agente de Voz - Tienda Online

## 📌 ¿Qué resuelve?
Backend de API REST que conecta un **agente de voz de ElevenLabs** con un sistema de e-commerce. Permite consultar productos y órdenes mediante conversaciones por voz.

---

## 🔁 Flujo breve

### 🛒 **get_products**
Webhook → Obtener Productos del backend → Respond con JSON

### 📦 **get_product_by_id** (get_order_by_id)
Webhook POST → Valida `orderId` → Consulta orden en backend → Respond con información de la orden  
- Si falta `orderId` → Error 400  
- Si no existe → Error 404  

---

## ⚡ Partes "sheites"

### 🎙️ **Integración con ElevenLabs**
Este workflow funciona como el **backend** del agente de voz creado en [ElevenLabs](https://elevenlabs.io/app/agents/agents).  
El agente de IA puede llamar a estas herramientas mediante webhooks protegidos con Header Auth.

### 🔐 **Autenticación Header Auth**
Todos los webhooks están protegidos con Header Auth de 32 caracteres, garantizando que solo el agente de ElevenLabs pueda consumir los endpoints.

### 🚀 **Túnel con Cloudflare**
Expone el backend local mediante Cloudflare Tunnel (`ron-lease-nerve-alternate.trycloudflare.com`), permitiendo que ElevenLabs acceda a los endpoints sin configurar hosting externo.

### ⚠️ **Manejo de errores**
Respuestas estructuradas según el caso:
- `400` → `orderId is required`  
- `404` → `orderId does not exists`  
- `200` → Información de la orden  

---

## 🏗️ Arquitectura

```
Usuario (Voz)
   ↓
[ElevenLabs Agent] ← Conversación por voz
   ↓
n8n (API REST) ← Webhooks con Header Auth
   ↓
Backend Tienda ← Consultas HTTP
```

---

## 📡 Endpoints

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/my-store-products` | Lista todos los productos |
| POST | `/my-store-orders` | Obtiene orden por ID |
