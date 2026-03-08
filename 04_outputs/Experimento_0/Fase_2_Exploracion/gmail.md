# Gmail — Exploración

## Helpers disponibles

| Helper | Descripción | Probado |
|---|---|---|
| `+triage` | Resumen de inbox no leído (tabla: fecha, remitente, id, subject) | Si |
| `+send` | Enviar email | No (pendiente) |
| `+watch` | Stream de nuevos emails como NDJSON | No |

## Comandos API probados

### `gws gmail +triage`
- Output en **formato tabla** por defecto (no JSON). Muy legible.
- Muestra los 20 emails no leídos más recientes con fecha, from, id y subject.
- Ideal para un agente que necesita priorizar inbox.

### `gws gmail users messages list --params '{"userId": "me", "maxResults": 3, "q": "from:reboot.academy"}'`
- Búsqueda por query (misma sintaxis que Gmail search).
- Devuelve solo ids y threadIds — necesita un `get` posterior para obtener contenido.
- Soporta paginación con `nextPageToken`.

### `gws gmail users labels list --params '{"userId": "me"}'`
- Lista todas las etiquetas: sistema (INBOX, SENT, SPAM...) y personalizadas.
- Las labels personalizadas revelan la estructura operativa del usuario (deals, proyectos, funnels).

### `gws gmail users threads list --params '{"userId": "me", "maxResults": 3}'`
- Lista hilos con snippet del contenido. Muy útil para contexto rápido.

## Recursos disponibles (via `users`)
drafts, history, labels, messages, settings, threads

## Valoración

| Dimensión | Nota |
|---|---|
| Usabilidad | Alta — `+triage` es excelente para uso diario |
| Agent-friendliness | Alta — JSON estructurado, queries potentes, paginación |
| Relevancia de negocio | Muy alta — email es central para follow-ups, ventas, propuestas |
| Potencial de automatización | Alto — `+watch` para monitoreo, queries para filtrado |
