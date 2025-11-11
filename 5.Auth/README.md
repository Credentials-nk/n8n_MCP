# 🔐 Auth — Métodos de autenticación en n8n

## 📌 ¿Qué incluye?
Ejemplo de webhook con autenticación JWT que demuestra cómo proteger endpoints y extraer información del usuario autenticado. También incluye guía de decisión para elegir el método de auth adecuado.

## 🔑 Los 3 tipos de Auth principales

### 1️⃣ **Basic Auth** (usuario:contraseña)
- **Cómo funciona**: Envía `Authorization: Basic base64(usuario:password)` en cada request
- **Cuándo usar**: Sistemas empresariales legacy, APIs que requieren user/pass
- **Ejemplos**: Jira, Jenkins, CRM antiguos, sistemas internos corporativos
- **En n8n**: Webhook node → Authentication → Basic Auth

### 2️⃣ **Header Auth** (API Key/Token)
- **Cómo funciona**: Envía token en header personalizado (`X-API-Key`, `Authorization: Bearer token`)
- **Cuándo usar**: APIs modernas con tokens/keys, webhooks de terceros
- **Ejemplos**: Slack, Discord, Stripe, APIs REST personalizadas
- **En n8n**: Webhook node → Authentication → Header Auth

### 3️⃣ **JWT** (JSON Web Token)
- **Cómo funciona**: Token firmado que contiene metadatos del usuario (roles, permisos, tiempo de expiración)
- **Cuándo usar**: Microservicios, apps que necesitan info del usuario, permisos granulares
- **Ejemplos**: Backends de apps móviles, sistemas con roles/permisos, autenticación temporal
- **En n8n**: Webhook node → Authentication → JWT Auth

## 🚀 Ejemplo JWT en acción
Este workflow demuestra JWT:

**Request**:
```bash
GET /company/metrics/123
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response**:
```json
{
  "companyId": "123",
  "sales": 3452231,
  "user": {
    "name": "Juan Pérez",
    "isAdmin": true,
    "roles": ["admin", "viewer"]
  },
  "payload": "{ JWT payload completo }"
}
```

El JWT se decodifica automáticamente y los datos del usuario (`jwtPayload.name`, `jwtPayload.admin`) se usan para personalizar la respuesta.

## 🤔 ¿Cuál elegir?

1. **¿El servicio externo ya soporta un método específico?** → Usa ese
2. **¿Necesitas info del usuario o permisos?** → **JWT**
3. **¿Tienes una API key simple?** → **Header Auth**
4. **¿Sistema legacy con user/pass?** → **Basic Auth**

## 💡 Tip práctico
**Header Auth** es el más flexible: funciona con cualquier token/key y puedes personalizar el nombre del header según la API que consumas.

---

*Basado en la guía de [Nate Herk | AI Automation](https://www.youtube.com/watch?v=3FfCRbq3XMs)*