# 🛠️ Tools — Herramientas reutilizables para Agentes AI

## 📌 ¿Qué son las Tools?
Workflows modulares diseñados para ser invocados por Agentes AI como "herramientas" que amplían sus capacidades. Cada tool es un sub-workflow que recibe parámetros, ejecuta una tarea específica (API call, cálculo, transformación) y retorna un resultado estructurado.

## 🎯 ¿Para qué sirven?
- **Extender agentes**: permite que LLMs realicen acciones concretas (consultar APIs, bases de datos, servicios externos)
- **Reutilización**: un mismo tool puede usarse en múltiples agentes o workflows
- **Modularidad**: separa la lógica de negocio del agente (el LLM decide cuándo usarla)
- **Testeo aislado**: cada tool se prueba independientemente antes de integrarlo

## 🔧 Estructura típica de un Tool
1. **Execute Workflow Trigger**: entrada con schema JSON (define parámetros esperados)
2. **Lógica de negocio**: HTTP Request, DB query, transformación de datos
3. **Normalización**: Set/Edit Fields para retornar estructura limpia y consistente
4. **Output**: JSON con los campos relevantes que el agente necesita

---

## 📦 Tools disponibles

### 🔍 Pokemon_finder_Tool
**Propósito**: Consulta información de Pokémon desde PokeAPI.

**Input**:
```json
{
  "name": "Pikachu"
}
```

**Proceso**:
1. Recibe nombre del Pokémon (normaliza: trim + lowercase)
2. HTTP Request a `https://pokeapi.co/api/v2/pokemon/{name}`
3. Extrae y estructura campos relevantes

**Output**:
```json
{
  "id": 25,
  "name": "pikachu",
  "sprites.back_default": "https://...",
  "sprites.front_default": "https://...",
  "weight": 60,
  "types": ["electric"]
}
```

**Uso en agente**: El LLM puede invocar esta tool cuando el usuario pregunta sobre un Pokémon específico.

---

## 🚀 Cómo crear un nuevo Tool

1. **Define el schema de entrada**: usa Execute Workflow Trigger con `jsonExample`
2. **Implementa la lógica**: HTTP, DB, transformación, etc.
3. **Normaliza la salida**: usa Set/Edit Fields para estructura consistente
4. **Prueba aislado**: ejecuta manualmente con datos de ejemplo
5. **Integra en agente**: conecta como tool en el AI Agent node

## 💡 Buenas prácticas
- **Input explícito**: schema claro con tipos y ejemplos
- **Output limpio**: solo campos necesarios, sin anidación excesiva
- **Manejo de errores**: validar input y capturar errores de API/DB
- **Nombres descriptivos**: `Pokemon_finder`, `Weather_checker`, `Email_validator`
- **Documentación inline**: comentarios en nodos complejos

## 🔗 Integración con Agentes
En el AI Agent node:
1. Agregar tool → "Execute Workflow"
2. Seleccionar el workflow del tool
3. El LLM decide automáticamente cuándo invocar la tool según el contexto de la conversación

---

**Concepto clave**: Las tools transforman agentes de "solo conversación" en "agentes con acciones" capaces de interactuar con el mundo real.
