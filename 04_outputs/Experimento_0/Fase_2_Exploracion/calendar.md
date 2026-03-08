# Calendar — Exploración

## Helpers disponibles

| Helper | Descripción | Probado |
|---|---|---|
| `+agenda` | Próximos eventos de todos los calendarios (tabla) | Si |
| `+insert` | Crear evento nuevo | No |

## Comandos API probados

### `gws calendar +agenda`
- Output en **formato tabla**: calendar, end, location, start, summary.
- Muestra eventos de hoy y próximos días. Muy útil para contexto diario.

### `gws workflow +standup-report`
- Combina Calendar + Tasks. Devuelve JSON con reuniones del día + tareas abiertas.
- Output muy agent-friendly: `meetingCount`, array de meetings con start/end/summary.

## Recursos disponibles
events, calendarList, acl, freebusy, settings, channels, colors

## Valoración

| Dimensión | Nota |
|---|---|
| Usabilidad | Alta — `+agenda` da contexto inmediato del día |
| Agent-friendliness | Alta — JSON estructurado, fácil de combinar con otros servicios |
| Relevancia de negocio | Alta — preparación de reuniones, coordinación, follow-ups |
| Potencial de automatización | Alto — meeting prep, recordatorios, reportes diarios |
