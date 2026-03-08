# Drive — Exploración

## Helpers disponibles

| Helper | Descripción | Probado |
|---|---|---|
| `+upload` | Subir fichero con metadata automática | No |

## Comandos API probados

### `gws drive files list --params '{"pageSize": 5}'`
- Lista ficheros con id, mimeType, name. Output JSON limpio.
- Soporta `orderBy`, `q` (queries), paginación.

### Búsqueda por tipo: spreadsheets
```bash
gws drive files list --params '{"pageSize": 5, "q": "mimeType=\"application/vnd.google-apps.spreadsheet\"", "orderBy": "modifiedTime desc"}'
```
- Funciona perfectamente. Permite filtrar por tipo MIME, fecha, nombre, etc.
- Query syntax potente (misma que Drive API).

### Búsqueda por tipo: documents
- Mismo patrón con `mimeType="application/vnd.google-apps.document"`.
- Devuelve docs recientes ordenados por modificación.

## Recursos disponibles
files, permissions, comments, replies, revisions, drives, changes, accessproposals, approvals, apps, channels, operations, teamdrives

## Valoración

| Dimensión | Nota |
|---|---|
| Usabilidad | Alta — queries de búsqueda potentes y familiares |
| Agent-friendliness | Alta — JSON estructurado, ids para encadenar operaciones |
| Relevancia de negocio | Muy alta — propuestas, docs comerciales, ficheros compartidos |
| Potencial de automatización | Alto — búsqueda, organización, permisos, monitoreo de cambios |
