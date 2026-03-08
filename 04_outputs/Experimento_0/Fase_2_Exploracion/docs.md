# Docs — Exploración

## Helpers disponibles

| Helper | Descripción | Probado |
|---|---|---|
| `+write` | Añadir texto a un documento | No |

## Comandos API probados

### `gws docs documents get --params '{"documentId": "<ID>"}' --format yaml`
- Devuelve la estructura completa del documento (body, content, paragraphs, text runs).
- Formato muy detallado: incluye estilos, headings, índices de posición.
- Soporta formatos json, yaml, csv, table.

### Observaciones
- El output es el modelo de datos interno de Google Docs (no texto plano).
- Para un agente que necesita solo el texto, hay que extraerlo de los `textRun.content`.
- Para un agente que necesita modificar el doc, la estructura de posiciones (startIndex/endIndex) es necesaria.

## Recursos disponibles
documents

## Valoración

| Dimensión | Nota |
|---|---|
| Usabilidad | Media — el output raw es muy verboso para uso manual |
| Agent-friendliness | Media-alta — estructurado pero requiere parseo para extraer texto |
| Relevancia de negocio | Alta — propuestas, dossiers, documentos comerciales |
| Potencial de automatización | Medio — `+write` para añadir texto; leer requiere parseo adicional |
