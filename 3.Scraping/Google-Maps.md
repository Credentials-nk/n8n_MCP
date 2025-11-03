# 🗺️ Google-Maps — Prospección y enriquecimiento de negocios

## 📌 ¿Qué resuelve?
Toma consultas (Query + Location) desde una Google Sheet, ejecuta un scraping de Google Maps (Apify), guarda los negocios encontrados y, para los que tienen sitio web válido, raspa el sitio (Firecrawl) para extraer emails y redes sociales. Todo queda registrado en hojas separadas y la consulta se marca como procesada. Corre cada 30 minutos.

## 🔁 Flujo breve
1) Trigger (cada 30 min) → lee pendientes en "Query"
2) Lanza actor de Apify (Google Places) y espera hasta terminar (wait + loop de estado)
3) Descarga resultados → guarda en hoja "Data"
4) Filtra negocios con website (excluye IG)
5) Procesa por lotes (SplitInBatches): scrapea sitio con Firecrawl → extrae emails/LinkedIn/Facebook/Instagram/Twitter (Code Node)
6) Guarda detalles en "Details" → marca la fila de "Query" como procesada

## ⚡ Partes “sheites” (lo clave)
- Orquestación asíncrona Apify: start → wait → poll status → fetch dataset (patrón robusto de jobs externos)
- Fan‑out por lotes: controla ritmo y rate‑limits al procesar websites uno a uno
- Extractor de contacto robusto: regex de emails + normalización y detección de redes (URLs y @handles)
- Persistencia clara: "Data" (negocios) y "Details" (contactos), vinculados por website; "Query" se marca por row_number
- Filtro de calidad: descarta websites vacíos o IG-only para priorizar sitios corporativos útiles

## 📑 Entradas y salidas
- Entrada (Sheet "Query"): columnas `Query`, `Location`, `Status`, `row_number`
- Salida (Sheet "Data"): `title`, `categoryName`, `address`, `phone`, `website`, `searchString`, `status`
- Salida (Sheet "Details"): `website`, `emails`, `linkedin`, `facebook`, `instagram`, `twitter`

## 🔒 Notas rápidas
- Credenciales: Bearer tokens en credenciales seguras (Apify / Firecrawl). Evitar tokens en texto plano.
- Límites: respeta rate‑limits; usa lotes para no saturar APIs.
- Re‑ejecución: al marcar `Status=true` en "Query", evitas reprocesar la misma fila.
