# Resultado Fase 3 — Casos de uso básicos

**Fecha:** 2026-03-08
**Estado:** Completada

---

## Casos ejecutados

### Caso 1 — Gmail: Triaje inteligente de inbox
**Flujo:** Filtrar emails no leídos excluyendo promos/social → obtener headers de uno relevante → leer snippet.

**Comandos usados:**
1. `messages list` con query `is:unread label:inbox -category:promotions -category:social -category:forums`
2. `messages get` con `format: metadata` + jq para extraer From/Subject/Date
3. `messages get` con jq `.snippet` para leer el contenido

**Resultado:** Encontrado email de coordinación microcredenciales ULPGC <> Edukami. Flujo completo funcional.

**Valoración:** Alta. Un agente puede hacer triaje de inbox, clasificar por relevancia y resumir en segundos.

---

### Caso 2 — Drive: Buscar propuestas comerciales
**Flujo:** Buscar ficheros con "propuesta" en el nombre → obtener links directos.

**Comando:** `drive files list` con query `name contains 'propuesta'`, fields `id,name,mimeType,modifiedTime,webViewLink`.

**Resultado:** 10 propuestas encontradas: Microcredenciales IA, Piloto Edukami/Moodle, Resumen SPEGC, Ecoluz, bitCRM, Asesoría IA, etc. Con links directos a cada una.

**Fricción:** Escapar comillas simples dentro de JSON dentro de shell es un dolor. Solución: usar double-quote escaping con `\"` en vez de single quotes anidadas.

**Valoración:** Muy alta. Búsqueda de docs de negocio es un caso de uso killer. Con `webViewLink` un agente puede devolver links clickeables.

---

### Caso 3 — Calendar: Contexto del día
**Flujo:** Listar eventos de hoy con asistentes y descripción.

**Comando:** `calendar events list` con `timeMin/timeMax` del día + jq para extraer summary, start, end, attendees, description.

**Resultado:** 7 eventos del día con toda la info. Los eventos personales no tienen asistentes ni descripción (normal).

**Valoración:** Alta. Para reuniones con clientes (que sí tienen asistentes, links, docs adjuntos) el valor sube mucho.

---

### Caso 4 — Sheets: Leer estado de funnel
**Flujo:** Leer datos de funnel + metadata de la spreadsheet.

**Comandos:**
1. `sheets +read` con rango `A1:E10`
2. `spreadsheets get` con jq para título y nombres de hojas

**Resultado:** Funnel Transfiere con 9 leads de universidades/organismos, columnas: Nombre, Organización, Puesto, Descripción, Interés. Todos etiquetados como "Skilland MicroCred".

**Valoración:** Muy alta. Un agente puede leer funnels, contar leads por categoría, detectar cambios, generar reportes.

---

### Caso 5 — Docs: Extraer texto de propuesta
**Flujo:** Obtener documento completo → extraer texto plano con jq.

**Comando:** `docs documents get` + jq `[.body.content[].paragraph?.elements[]?.textRun?.content // empty] | join("")`

**Resultado:** Texto completo de la Propuesta Microcredenciales IA extraído. Incluye fundamentación de carga lectiva, descripción de expertos universitarios, estructura de programa.

**Valoración:** Alta. El truco de jq para extraer texto funciona bien. Un agente puede leer y resumir propuestas automáticamente.

---

### Caso 6 — Workflow: Meeting prep
**Flujo:** `gws workflow +meeting-prep` para preparar próxima reunión.

**Resultado:** Devuelve JSON con summary, start/end, attendees, description, hangoutLink, htmlLink. La próxima reunión era "Agentic Biz I+D" (sin asistentes externos).

**Valoración:** Media-alta. Para reuniones con clientes con description y docs linkados sería muy potente. Para bloques personales, el valor es menor.

---

### Caso 7 — Gmail por labels de negocio
**Flujo:** Buscar emails con label "01.- Deals Loopa" → leer headers y snippet de deals activos.

**Comandos:**
1. `messages list` con query `label:01.- Deals Loopa`
2. Loop de `messages get` con jq para extraer from, subject, snippet

**Resultado:** Encontrados emails de deals activos: propuesta "AI-First" con Yanira (follow-up), y mentoring IA360 con Jacob vía Loopa Labs.

**Valoración:** Muy alta. Labels como sistema de clasificación de deals + búsqueda por CLI = CRM-adjacent funcional.

---

## Fricciones encontradas

1. **Escapado de queries**: mezclar JSON con queries de Drive/Gmail dentro de shell es propenso a errores. Las comillas simples dentro de JSON single-quoted no funcionan. Hay que usar double-quote escaping.
2. **Gmail messages list solo devuelve IDs**: siempre necesitas un `get` posterior. Para un agente no es problema (2 llamadas), pero para uso manual añade fricción.
3. **Meeting prep limitado por datos del evento**: si el evento no tiene description, attendees o docs, el output es pobre. El valor depende de la riqueza de los eventos.

---

## Patrones útiles descubiertos

| Patrón | Comando |
|---|---|
| Triaje inbox sin ruido | `q: "is:unread label:inbox -category:promotions -category:social"` |
| Buscar docs por nombre con link | `fields: "files(id,name,mimeType,modifiedTime,webViewLink)"` |
| Extraer texto de Google Doc | `jq '[.body.content[].paragraph?.elements[]?.textRun?.content // empty] | join("")'` |
| Leer headers de email | `format: metadata` + jq select por nombre de header |
| Buscar por label de negocio | `q: "label:NOMBRE-DE-LABEL"` |

---

## Criterios de avance — verificación

- [x] Al menos 5 casos de uso básicos ejecutados y documentados (7 de 7)
- [x] Cada caso incluye: comando, output, valoración de utilidad, fricciones
- [x] Confianza construida en los primitivos antes de combinar

**Fase 3 cerrada. Lista para avanzar a Fase 4.**
