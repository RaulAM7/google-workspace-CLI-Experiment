# Sheets — Exploración

## Helpers disponibles

| Helper | Descripción | Probado |
|---|---|---|
| `+read` | Leer valores de un rango | Si |
| `+append` | Añadir fila a una spreadsheet | No |

## Comandos API probados

### `gws sheets +read --spreadsheet '<ID>' --range 'A1:E5'`
- Devuelve JSON con `majorDimension`, `range` y `values` (array de arrays).
- Output perfecto para procesamiento por agentes.
- Nota: el flag es `--spreadsheet`, no `--spreadsheet-id`.

### Output de ejemplo (funnel Transfiere)
```json
{
  "majorDimension": "ROWS",
  "range": "Transfiere_2026_Funnel_actualizado_v2!A1:E5",
  "values": [
    ["Nombre", "Organizacion", "Puesto", "Descripcion", "INTERES PARA NOSOTROS"],
    ["Alicia Ardenuy Zabala", "Universitat Oberta de Catalunya", "...", "...", "Skilland MicroCred"],
    ...
  ]
}
```

## Recursos disponibles
spreadsheets (values, sheets, developerMetadata)

## Valoración

| Dimensión | Nota |
|---|---|
| Usabilidad | Alta — helpers simplifican mucho leer/escribir |
| Agent-friendliness | Muy alta — arrays de arrays son triviales de parsear |
| Relevancia de negocio | Muy alta — funnels, tracking de pagos, tareas, CRM-adjacent |
| Potencial de automatización | Muy alto — leer funnels, añadir datos, reportes automáticos |
