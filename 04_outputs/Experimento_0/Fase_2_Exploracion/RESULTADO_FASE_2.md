# Resultado Fase 2 — Exploración por herramienta

**Fecha:** 2026-03-08
**Estado:** Completada

---

## Resumen de exploración

| Producto | Explorado | Helpers probados | Comandos API probados | Valoración general |
|---|---|---|---|---|
| Gmail | Si | `+triage` | messages list, labels list, threads list | Muy alta |
| Drive | Si | — | files list (con queries y filtros) | Muy alta |
| Calendar | Si | `+agenda` | via `+standup-report` (workflow) | Alta |
| Sheets | Si | `+read` | — | Muy alta |
| Docs | Si | — | documents get | Media-alta |

---

## Ranking de productos por valor de negocio

### Tier 1 — Impacto inmediato
1. **Gmail** — `+triage` es killer feature. Queries potentes para filtrar emails. Combina perfectamente con flujos de follow-up y ventas.
2. **Sheets** — `+read` y `+append` son operaciones core para funnels, tracking, CRM-adjacent. Output trivial de parsear.
3. **Drive** — Búsqueda de ficheros por tipo, fecha, nombre. Base para encontrar propuestas, docs compartidos, etc.

### Tier 2 — Alto valor con combinaciones
4. **Calendar** — `+agenda` da contexto diario. Muy valioso combinado con Gmail y Drive para meeting prep.

### Tier 3 — Útil pero con fricción
5. **Docs** — Output verboso (modelo de datos interno). Para leer texto se necesita parseo. `+write` promete pero no probado aún.

---

## Descubrimientos clave

### Los helpers (`+`) son el diferenciador
Los helpers convierten operaciones de múltiples pasos API en comandos de una línea. Son lo que hace a `gws` realmente agent-friendly y usable para no-ingenieros.

### Los workflows cross-service son muy prometedores
`+standup-report` demostró que combinar Calendar + Tasks en un solo comando JSON funciona perfectamente. Los otros workflows (`+meeting-prep`, `+email-to-task`, `+weekly-digest`) son exactamente los flujos de negocio que buscamos.

### Formatos de output flexibles
- `json` (default) — para agentes y programmatic use
- `table` — para uso humano rápido (`+triage`, `+agenda`)
- `yaml` — para inspección detallada
- `csv` — para importar en otras herramientas

### La sintaxis de queries de Drive y Gmail es potente
Se usa la misma sintaxis que las APIs nativas de Google (Drive search, Gmail search). Esto significa que cualquier query que funcione en Gmail web funciona aquí.

---

## Fricciones encontradas

1. **IDs obligatorios**: `gws` no acepta URLs ni nombres de fichero, solo IDs. Hay que extraer el ID manualmente de la URL (entre `/d/` y `/edit`). Muy incómodo para uso manual. Se mitiga con agentes o encadenando búsqueda Drive + jq.
2. **Docs API solo para docs nativos**: ficheros importados (Word, PDF subidos a Drive) devuelven 404. Solo funciona con Google Docs nativos.
3. **Sheets**: el flag es `--spreadsheet`, no `--spreadsheet-id` (naming inconsistente).
4. **Docs**: el output de `documents get` es el modelo de datos interno, no texto plano. Necesita parseo.
5. **Gmail messages list**: devuelve solo ids, necesita un `get` posterior para contenido completo.
6. **Uso manual es incómodo**: sin un agente intermediario, la experiencia tiene mucha fricción (copiar IDs, construir JSON para params, etc.). La herramienta brilla más como capa para agentes que como CLI para humanos.

---

## Criterios de avance — verificación

- [x] Al menos 3 productos explorados con comandos documentados (5 de 5)
- [x] Para cada producto: lista de comandos, outputs de ejemplo, valoración inicial
- [x] Identificados los productos más prometedores: Gmail, Sheets, Drive (Tier 1)

**Fase 2 cerrada. Lista para avanzar a Fase 3.**
