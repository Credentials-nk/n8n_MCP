# 📋 Sección RRHH - Workflows de Recursos Humanos

Esta sección contiene workflows de n8n especializados para la gestión automatizada de procesos de Recursos Humanos, facilitando la administración de solicitudes de tiempo libre y ausencias por motivos médicos.

## 🎯 Propósito

Los workflows de esta sección están diseñados para:
- **Automatizar** los procesos de solicitud de vacaciones y partes médicos
- **Validar** automáticamente las solicitudes según reglas de negocio
- **Notificar** al departamento de RRHH para aprobaciones necesarias
- **Gestionar** calendarios corporativos y bases de datos de empleados
- **Comunicar** automáticamente las decisiones a los solicitantes

## 📁 Contenido de la Sección

### 1. `Solicitud-parte-medico.json`
**Workflow completo para gestión de ausencias por motivos médicos y vacaciones**

#### 🔧 Funcionalidades:
- **Formulario de solicitud** con campos para:
  - Tipo de solicitud (Vacaciones/Parte Médico)
  - Datos del empleado (nombre, email)
  - Fechas de inicio y retorno
  - Comentarios adicionales

#### ✅ Validaciones automáticas:
- **Antelación mínima**: Verifica que las vacaciones se soliciten con 7 días de anticipación
- **Fechas coherentes**: Valida que la fecha de retorno sea posterior a la de inicio
- **Balance de días**: Consulta base de datos para verificar días disponibles
- **Límites por tipo**: Diferencia entre días de vacaciones y días por enfermedad

#### 🔄 Flujos de aprobación:
- **Vacaciones**: Requieren aprobación de RRHH con formulario de 48 horas
- **Partes médicos**: Se procesan automáticamente (solo notificación a RRHH)

#### 🗃️ Integraciones:
- **Supabase**: Base de datos para gestión de días disponibles por empleado
- **Google Calendar**: Creación automática de eventos en calendario corporativo
- **Gmail**: Notificaciones por email al empleado y RRHH
- **Discord**: Alertas en tiempo real al canal de RRHH

### 2. `Solicitud-tiempo-libre.json`
**Workflow simplificado para solicitudes de vacaciones únicamente**

#### 🔧 Funcionalidades:
- Formulario básico de solicitud de tiempo libre
- Validaciones similares al workflow principal
- Proceso de aprobación manual por RRHH
- Actualización automática de base de datos y calendarios

#### 💡 Diferencias con el workflow principal:
- **Solo vacaciones**: No incluye gestión de partes médicos
- **Interfaz simplificada**: Menor complejidad en el formulario
- **Proceso único**: Un solo flujo de validación y aprobación

## 🔗 Integraciones y Dependencias

### Bases de Datos:
- **Supabase**: Tabla `days_of` con información de días disponibles por empleado
- **PostgreSQL**: Conexión directa para consultas de empleados (tabla `user`)

### Servicios Externos:
- **Google Calendar API**: Gestión de eventos en calendario corporativo
- **Gmail API**: Envío de notificaciones por correo electrónico
- **Discord Webhook**: Notificaciones instantáneas a canal de RRHH

### Credenciales Necesarias:
- Supabase API Key
- Google OAuth2 (Calendar + Gmail)
- Discord Webhook URL
- PostgreSQL Database credentials

## 🚀 Configuración e Implementación

### Prerrequisitos:
1. **n8n instalado** y configurado
2. **Cuentas activas** en Supabase, Google Cloud Console, Discord
3. **Base de datos configurada** con las tablas necesarias:
   - `days_of`: Gestión de días disponibles por empleado
   - `user`: Información de empleados

### Pasos de configuración:
1. **Importar workflows** en tu instancia de n8n
2. **Configurar credenciales** para cada servicio integrado
3. **Personalizar formularios** según políticas de la empresa
4. **Ajustar validaciones** según reglas de negocio específicas
5. **Probar workflows** con casos de uso reales

## 📊 Esquema de Base de Datos

### Tabla `days_of`:
```sql
- id (Primary Key)
- employee_id (Foreign Key)
- vacation_days (Integer)
- sick_days (Integer)
- email (String)
```

### Tabla `user`:
```sql
- id (Primary Key)
- name (String)
- email (String)
- department (String)
```

## 📈 Beneficios del Sistema

### Para Empleados:
- ✅ **Solicitudes 24/7**: Pueden realizar solicitudes en cualquier momento
- ✅ **Validación inmediata**: Conocen al instante si su solicitud es viable
- ✅ **Notificaciones automáticas**: Reciben confirmaciones por email

### Para RRHH:
- ✅ **Proceso automatizado**: Reducción significativa de trabajo manual
- ✅ **Validaciones automáticas**: El sistema verifica reglas antes de llegar a RRHH
- ✅ **Trazabilidad completa**: Historial detallado de todas las solicitudes
- ✅ **Integración calendarios**: Actualización automática de planificación

### Para la Organización:
- ✅ **Eficiencia operativa**: Procesos más rápidos y confiables
- ✅ **Reducción de errores**: Eliminación de errores manuales de cálculo
- ✅ **Compliance**: Asegurar cumplimiento de políticas de la empresa
- ✅ **Reporting automático**: Datos estructurados para análisis y reportes

## 🛠️ Personalización

Estos workflows pueden adaptarse fácilmente para:
- **Diferentes tipos de ausencia**: Agregar categorías adicionales
- **Reglas de negocio específicas**: Modificar validaciones según políticas
- **Integraciones adicionales**: Conectar con otros sistemas corporativos
- **Flujos de aprobación complejos**: Implementar múltiples niveles de autorización

## 🔒 Seguridad y Privacidad

- **Datos encriptados** en tránsito y reposo
- **Acceso controlado** mediante credenciales seguras
- **Logs de auditoría** para trazabilidad completa
- **Validación de datos** en cada etapa del proceso

---

*Estos workflows representan una solución integral para la modernización de procesos de RRHH, proporcionando automatización, eficiencia y mejor experiencia tanto para empleados como para el departamento de recursos humanos.*