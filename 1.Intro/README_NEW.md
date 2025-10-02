# 🚀 Introducción a n8n - Workflows Básicos

Este directorio contiene **3 workflows fundamentales** que sirven como introducción práctica a n8n y demuestran diferentes patrones de automatización.

---

## 📋 Workflows Disponibles

### 1. 👕 **Google Forms - T-shirt** 
**Archivo:** `Google Forms - T-shirt.json`

**¿Qué hace?**  
Sistema básico que lee solicitudes de camisetas desde un Google Form (almacenadas en Google Sheets), filtra las solicitudes que tienen email válido, y consulta el inventario disponible en otra hoja de cálculo.

**Servicios utilizados:**
- Google Sheets (lectura de 2 hojas: solicitudes e inventario)
- OAuth 2.0 para autenticación con Google

**Propósito:**  
Ejemplo introductorio de cómo conectar múltiples hojas de Google Sheets, aplicar filtros básicos y agregar datos.

---

### 2. 👕 **Google Forms - T-shirt PSQL** 
**Archivo:** `Google Forms - T-shirt PSQL.json`

**¿Qué hace?**  
Sistema completo y automatizado que se ejecuta cuando alguien llena el formulario. Valida la solicitud, verifica existencias en una base de datos PostgreSQL, descuenta del inventario si hay stock disponible, actualiza el estado en Google Sheets (verificado o fallido) y envía un correo de resumen con todos los cambios realizados.

**Servicios utilizados:**
- Google Sheets Trigger (detección automática de nuevas filas)
- PostgreSQL (control de inventario)
- Gmail (notificaciones por email)
- Google Sheets (actualización de estados)

**Propósito:**  
Workflow avanzado que muestra cómo integrar una base de datos, manejar triggers automáticos, procesar datos en lotes y enviar notificaciones, ideal para procesos de negocio reales.

---

### 3. 🐾 **Pokemon Workflow**
**Archivo:** `pokemon-workflow.json`

**¿Qué hace?**  
Lee una lista de IDs de Pokémon desde Google Sheets, filtra los que no tienen información completa, consulta la API pública de PokeAPI para obtener sus datos (nombre, tipo, sprites), actualiza la información en Google Sheets y envía un correo con el resumen de Pokémons procesados.

**Servicios utilizados:**
- Google Sheets (lectura y escritura)
- PokeAPI (API REST pública)
- Gmail (reportes por email)
- HTTP Request (consultas a API externa)

**Propósito:**  
Ejemplo práctico de integración con APIs externas, procesamiento de respuestas JSON, extracción de datos específicos y actualización masiva de información.

---

## 🎯 ¿Para qué sirven estos workflows?

Estos 3 workflows son la **base fundamental** para entender:

- ✅ Cómo conectar servicios de Google (Sheets, Gmail)
- ✅ Integración con bases de datos (PostgreSQL)
- ✅ Consumo de APIs externas
- ✅ Triggers automáticos vs ejecución manual
- ✅ Filtrado y transformación de datos
- ✅ Notificaciones y reportes automatizados

Son workflows **didácticos y funcionales** que pueden adaptarse fácilmente a otros casos de uso similares.
