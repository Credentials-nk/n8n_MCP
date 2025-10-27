# 🔗 Solicitud-webhooks - Workflow con API REST

## 📋 Descripción General

Este workflow representa una **versión avanzada** del sistema de gestión de solicitudes de tiempo libre, implementando una arquitectura basada en **webhooks y API REST**. A diferencia de las versiones con formularios integrados, este workflow permite la integración con sistemas externos mediante endpoints HTTP.

## 🎯 Propósito

Proporcionar una **API REST completa** para que sistemas externos (aplicaciones móviles, portales web, sistemas internos) puedan enviar solicitudes de vacaciones y partes médicos de forma programática, manteniendo todas las validaciones y flujos de aprobación automatizados.

---

## 🚀 Características Principales

### 1. **API REST con Webhook Trigger**
- **Endpoint**: `POST /vacation/ask/form/001`
- **Autenticación**: Header Authentication (seguridad mediante token)
- **Respuestas HTTP**: Códigos de estado apropiados (200, 400)
- **Formato**: JSON Request/Response

### 2. **Validación Exhaustiva de Entrada**
El workflow incluye validaciones robustas mediante código JavaScript:

#### Validaciones de Solicitud:
- ✅ **Nombre completo**: Campo requerido
- ✅ **Email**: Formato válido y requerido
- ✅ **Fechas**: Formato ISO (YYYY-MM-DD) y coherencia temporal
- ✅ **Comentarios**: Límite de 500 caracteres (opcional)
- ✅ **Fechas lógicas**: La fecha de inicio debe ser anterior a la de retorno

#### Validaciones de Respuesta RRHH:
- ✅ **Aprobación**: Valor booleano requerido
- ✅ **Mensaje de rechazo**: Obligatorio cuando se desaprueba

### 3. **Dos Flujos Diferenciados**

#### 🏖️ **Flujo de Vacaciones**:
1. Validación de antelación mínima (7 días)
2. Verificación de días disponibles
3. Notificación a RRHH vía Discord
4. Espera de aprobación (webhook de continuación - 48 horas)
5. Validación de parámetros de respuesta
6. Creación de evento en Google Calendar
7. Actualización de base de datos
8. Notificación por email al empleado

#### 🏥 **Flujo de Parte Médico**:
1. Validación de fechas coherentes
2. Verificación de días por enfermedad disponibles
3. Notificación automática a RRHH (solo informativo)
4. Creación inmediata de evento en calendario
5. Actualización automática de base de datos
6. Confirmación por email al empleado

---

## 🔧 Arquitectura Técnica

### **Componentes del Workflow**

#### 1️⃣ **Trigger & Validation Layer**
```
Webhook Trigger → Validar Request → Verificar Campos → Respond to Webhook
```
- Recibe solicitudes HTTP POST
- Valida estructura y datos
- Responde inmediatamente al cliente (async processing)

#### 2️⃣ **Business Logic Layer**
```
Extraer Variables → Validar Tipo → Validaciones de Negocio → Consultar BD
```
- Determina tipo de solicitud (Vacaciones/Médico)
- Aplica validaciones específicas según el tipo
- Consulta disponibilidad en Supabase

#### 3️⃣ **Approval Layer (Solo Vacaciones)**
```
Notificar Discord → Wait Node (Webhook) → Validar Parámetros → Decisión RRHH
```
- Sistema de espera asíncrono con webhook de continuación
- Validación de parámetros de respuesta
- Manejo de errores en parámetros

#### 4️⃣ **Action & Notification Layer**
```
Crear Evento Calendar → Actualizar BD → Enviar Email → Fin
```
- Acciones finales según aprobación/rechazo
- Notificaciones multicanal

---

## 🛠️ Partes Clave del Workflow

### 🔐 **1. Seguridad y Autenticación**

**Webhook con Header Authentication:**
```javascript
// Requiere header HTTP
Authorization: Bearer <token>
```
- Protege el endpoint contra accesos no autorizados
- Reutilizable mediante credencial compartida

---

### ✅ **2. Validación de Request (JavaScript)**

**Nodo: "Validar request webhook"**

```javascript
const src = $input.first().json.body;
const errors = [];

// Validar email
if (!email) errors.push('Correo electrónico es requerido.');
else if (!isEmail(email)) errors.push('Correo electrónico inválido.');

// Validar fechas formato ISO
if (!ISO_DATE_RE.test(startDateStr))
  errors.push('Fecha de inicio debe tener formato YYYY-MM-DD.');

// Comparación de fechas
if (!(startDate < endDate)) {
  errors.push('La fecha de inicio debe ser ANTES que la fecha de retorno.');
}
```

**Características:**
- ✅ Regex para validación de email
- ✅ Validación de formato ISO de fechas
- ✅ Comparación temporal de fechas
- ✅ Límite de caracteres en comentarios
- ✅ Array de errores acumulativos

---

### 🔄 **3. Respuesta Asíncrona con Webhook**

**Nodo: "Respond to Webhook"**

El workflow responde inmediatamente al cliente sin bloquear:

```javascript
// Respuesta exitosa (200)
{
  "valid": true,
  "status": 200,
  "data": { /* datos procesados */ }
}

// Respuesta con errores (400)
{
  "valid": false,
  "status": 400,
  "errors": ["Error 1", "Error 2"]
}
```

---

### ⏳ **4. Wait Node con Webhook de Continuación**

**Nodo: "Esperar a RRHH 48 hs"**

```
Configuración:
- Resume: webhook
- Authentication: headerAuth
- Method: POST
- Timeout: 2 days
```

**Funcionamiento:**
1. El workflow se pausa y genera una URL de continuación
2. RRHH recibe la URL en Discord
3. Al responder, envía POST a la URL de continuación
4. El workflow se reanuda con los datos de la respuesta

**Body esperado en continuación:**
```json
{
  "approve": true,  // o false
  "message": "Motivo del rechazo (opcional si aprueba)"
}
```

---

### 🎯 **5. Validación de Parámetros de Continuación**

**Nodo: "Validación de los parametros"**

```javascript
const message = getStr(src['message']);
const approve = getBool(src['approve']);

// Validar que approve esté presente
if (approve === null || approve === undefined)
  errors.push('El valor de aprobación es requerido.');

// Si no aprueba, mensaje es obligatorio
if (!approve && !message) 
  errors.push('El mensaje es requerido cuando se desaprueba.');
```

**Manejo de errores:**
- Si los parámetros son incorrectos, notifica a RRHH por Discord
- No continúa el flujo hasta recibir parámetros válidos

---

### 🔀 **6. Condicionales Inteligentes**

**Tipos de validaciones:**

```javascript
// 1. Validación de antelación (7 días)
$json.startDate.toDateTime().plus(7, 'days') >= $now

// 2. Validación de días positivos
$json.daysRequested > 0

// 3. Validación de balance disponible
$json.daysRequested <= $json.vacation_days

// 4. Decisión de aprobación RRHH
$json.data.approve === true
```

---

### 📧 **7. Templates HTML Profesionales**

**Emails con diseño corporativo:**

```html
<!-- Card con sombras múltiples -->
<div class="card" style="
  box-shadow: 
    0 8px 24px rgba(0,0,0,0.28),
    0 12px 32px rgba(0,0,0,0.22),
    0 2px 6px rgba(0,0,0,0.15);
">
  <!-- Contenido personalizado -->
</div>
```

**Variantes:**
- ✅ Email de aprobación (color verde)
- ❌ Email de rechazo (color rojo)
- 🏥 Email de parte médico (color específico)

---

### 📊 **8. Integración con Supabase**

**Operaciones de base de datos:**

```javascript
// Consultar días disponibles
GET /days_of?email=eq.{email}

// Actualizar balance de vacaciones
UPDATE days_of
SET vacation_days = vacation_days - daysRequested
WHERE id = {employee_id}

// Actualizar días por enfermedad
UPDATE days_of
SET sick_days = sick_days - daysRequested
WHERE id = {employee_id}
```

---

## 📡 Uso de la API

### **Solicitar Vacaciones/Parte Médico**

**Endpoint:**
```
POST https://n8n.tudominio.com/webhook/vacation/ask/form/001
```

**Headers:**
```
Content-Type: application/json
Authorization: Bearer <tu-token-secreto>
```

**Body (Vacaciones):**
```json
{
  "Nombre completo": "Juan Pérez",
  "Correo electrónico": "juan.perez@empresa.com",
  "Fecha inicio": "2025-11-15",
  "Fecha retorno": "2025-11-20",
  "Comentarios": "Vacaciones de fin de año",
  "Vacaciones / Parte Médico": "Vacaciones"
}
```

**Body (Parte Médico):**
```json
{
  "Nombre completo": "María González",
  "Correo electrónico": "maria.gonzalez@empresa.com",
  "Fecha inicio": "2025-10-28",
  "Fecha retorno": "2025-10-30",
  "Comentarios": "Gripe estacional",
  "Vacaciones / Parte Médico": "Medico"
}
```

**Respuesta Exitosa (200):**
```json
{
  "valid": true,
  "status": 200,
  "data": {
    "fullName": "Juan Pérez",
    "email": "juan.perez@empresa.com",
    "startDate": "2025-11-15",
    "endDate": "2025-11-20",
    "comments": "Vacaciones de fin de año"
  }
}
```

**Respuesta con Errores (400):**
```json
{
  "valid": false,
  "status": 400,
  "errors": [
    "Correo electrónico inválido.",
    "La fecha de inicio debe ser ANTES que la fecha de retorno."
  ]
}
```

---

### **Responder a Solicitud (RRHH)**

Cuando RRHH recibe la notificación en Discord, debe enviar:

**Endpoint:**
```
POST <URL_de_continuación_del_workflow>
```

**Headers:**
```
Content-Type: application/json
Authorization: Bearer <token-header-auth>
```

**Body (Aprobar):**
```json
{
  "approve": true,
  "message": "Aprobado por disponibilidad de equipo"
}
```

**Body (Rechazar):**
```json
{
  "approve": false,
  "message": "No hay cobertura suficiente en el período solicitado"
}
```

---

## 🎨 Diferencias con Otros Workflows

| Característica | Solicitud-webhooks | Solicitud-tiempo-libre | Solicitud-parte-medico |
|---|---|---|---|
| **Trigger** | Webhook REST API | Form Trigger | Form Trigger |
| **Autenticación** | Header Auth | Ninguna | Ninguna |
| **Validación Entrada** | JavaScript avanzado | Básica (form) | Básica (form) |
| **Respuesta Async** | Sí (inmediata) | No (síncrona) | No (síncrona) |
| **Aprobación RRHH** | Webhook continuación | Form de espera | Automático/Form |
| **Validación Parámetros** | Doble validación | Simple | Simple |
| **Manejo Errores** | Completo con notificaciones | Básico | Básico |
| **Integración Externa** | ✅ Sí | ❌ No | ❌ No |

---

## 🔥 Ventajas de Este Enfoque

### ✅ **Para Desarrolladores:**
- **API RESTful** estándar
- **Validaciones robustas** con mensajes de error claros
- **Respuestas inmediatas** sin bloqueo
- **Fácil integración** con cualquier frontend

### ✅ **Para RRHH:**
- **Doble validación** de parámetros de respuesta
- **Notificaciones de error** si responden incorrectamente
- **Trazabilidad completa** de todas las interacciones

### ✅ **Para la Organización:**
- **Escalabilidad**: Múltiples sistemas pueden consumir la API
- **Desacoplamiento**: Frontend independiente del backend
- **Seguridad**: Autenticación obligatoria
- **Monitoring**: Fácil seguimiento de requests/responses

---

## 🔒 Seguridad Implementada

1. **Header Authentication**: Token secreto requerido
2. **Validación de entrada**: Previene inyección de datos maliciosos
3. **Límite de caracteres**: Evita payloads excesivos
4. **Formato estricto**: Solo acepta estructura predefinida
5. **Timeout de aprobación**: 48 horas máximo de espera

---

## 🐛 Manejo de Errores

### **Errores de Validación Inicial:**
- Respuesta HTTP 400 con array de errores
- No se inicia el flujo de aprobación
- No se envían notificaciones

### **Errores en Parámetros de Continuación:**
- Notificación a RRHH vía Discord
- Detención del flujo hasta corrección
- Log del error para debugging

### **Errores de Integración:**
- Reintentos automáticos en nodos de Supabase
- Logs detallados en n8n
- Notificaciones de fallback

---

## 📈 Casos de Uso

1. **Portal Web de Empleados**: Interfaz web consume la API
2. **App Móvil Corporativa**: Solicitudes desde dispositivos móviles
3. **Sistema ERP Externo**: Integración con software de gestión empresarial
4. **Chatbot Corporativo**: Slack/Teams bot para solicitudes conversacionales
5. **Integraciones Zapier/Make**: Conectar con otros workflows

---

## 🔄 Flujo Completo de Ejecución

```
1. Cliente HTTP → POST /vacation/ask/form/001
2. Webhook Trigger → Captura request
3. Validar Request → JavaScript validations
4. ¿Válido? → NO → Respond 400 + errors → FIN
5. ¿Válido? → SÍ → Respond 200 + data → Continuar async
6. Extraer Variables → Normalizar datos
7. ¿Vacaciones o Médico? → Decisión
8. [VACACIONES] → Validar antelación → Balance → Notificar RRHH
9. Esperar Webhook → 48h timeout
10. RRHH responde → Validar parámetros
11. ¿Aprobado? → Calendario + BD + Email ✅
12. ¿Rechazado? → Email rechazo ❌
13. [MÉDICO] → Validar balance → Notificar RRHH → Auto-aprobado
14. Calendario + BD + Email ✅
```

---

## 🎓 Conceptos Avanzados Aplicados

1. **Asynchronous Workflows**: Respuesta inmediata + procesamiento en background
2. **Webhook Continuations**: Pause/Resume de ejecución
3. **Input Validation Layer**: Validación exhaustiva antes de procesamiento
4. **Error Handling Patterns**: Múltiples niveles de manejo de errores
5. **Separation of Concerns**: Validación → Lógica → Acción → Notificación
6. **RESTful API Design**: Endpoints, códigos HTTP, JSON payloads
7. **Dual Validation**: Validación en entrada y en continuación

---

## 🚀 Mejoras Potenciales

- [ ] Agregar autenticación OAuth2 para mayor seguridad
- [ ] Implementar rate limiting para prevenir abuso
- [ ] Añadir webhooks de notificación al cliente (callbacks)
- [ ] Incluir documentación OpenAPI/Swagger
- [ ] Agregar métricas y analytics de uso
- [ ] Implementar versionado de API (v1, v2, etc.)
- [ ] Añadir soporte para adjuntar certificados médicos
- [ ] Crear endpoints adicionales (GET status, CANCEL request, etc.)

---

## 📚 Documentación Técnica

### **Tecnologías Utilizadas:**
- n8n (Workflow Automation)
- JavaScript (Validaciones custom)
- Supabase (Base de datos)
- Google Calendar API
- Gmail API
- Discord Webhooks
- HTTP/REST Protocols

### **Nodos n8n Destacados:**
- `Webhook Trigger`: Entry point de la API
- `Respond to Webhook`: Respuestas HTTP
- `Code Node`: JavaScript para validaciones
- `If Node`: Lógica condicional
- `Wait Node`: Pausas con webhook continuation
- `Supabase Node`: Operaciones de BD
- `Google Calendar Node`: Gestión de eventos
- `Gmail Node`: Emails transaccionales
- `Discord Node`: Notificaciones instantáneas

---

**Este workflow representa la evolución hacia una arquitectura moderna basada en APIs, permitiendo la integración con cualquier sistema externo manteniendo toda la lógica de negocio centralizada y segura.**