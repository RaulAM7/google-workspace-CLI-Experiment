# Ejercicios Prácticos — Fase 2

Mini clase para trastear con cada herramienta antes de avanzar.
Ejecutar directamente en consola, uno por uno.

---

## 1. Gmail (6 ejercicios)

### 1.1 — Triaje rápido
Ver tu inbox no leído de un vistazo.
```bash
gws gmail +triage
```

### 1.2 — Buscar emails de alguien concreto
Busca los últimos 5 emails de un remitente que te interese.
```bash
gws gmail users messages list --params '{"userId": "me", "maxResults": 5, "q": "from:marc@vendeconcojones.com"}'
```

### 1.3 — Leer un email concreto
Coge un `id` del ejercicio anterior y lee su contenido.
```bash
gws gmail users messages get --params '{"userId": "me", "id": "<ID_DEL_EMAIL>"}'
```

### 1.4 — Buscar por subject
Busca emails que contengan una palabra clave en el asunto.
```bash
gws gmail users messages list --params '{"userId": "me", "maxResults": 5, "q": "subject:propuesta"}'
```

### 1.5 — Ver tus labels personalizadas
Solo las que tú has creado (filtrando las de sistema).
```bash
gws gmail users labels list --params '{"userId": "me"}' | jq '[.labels[] | select(.type == "user") | .name]'
```

### 1.6 — Leer un hilo completo
Coge un `threadId` y mira toda la conversación.
```bash
gws gmail users threads get --params '{"userId": "me", "id": "<THREAD_ID>"}'
```

---

## 2. Drive (5 ejercicios)

### 2.1 — Tus 10 ficheros más recientes
```bash
gws drive files list --params '{"pageSize": 10, "orderBy": "modifiedTime desc"}'
```

### 2.2 — Buscar por nombre
Busca ficheros que contengan una palabra en el nombre.
```bash
gws drive files list --params '{"pageSize": 5, "q": "name contains \"Skilland\""}'
```

### 2.3 — Solo presentaciones (Slides)
```bash
gws drive files list --params '{"pageSize": 5, "q": "mimeType=\"application/vnd.google-apps.presentation\"", "orderBy": "modifiedTime desc"}'
```

### 2.4 — Ficheros compartidos conmigo
```bash
gws drive files list --params '{"pageSize": 5, "q": "sharedWithMe=true", "orderBy": "modifiedTime desc"}'
```

### 2.5 — Buscar PDFs
```bash
gws drive files list --params '{"pageSize": 5, "q": "mimeType=\"application/pdf\"", "orderBy": "modifiedTime desc"}'
```

---

## 3. Calendar (4 ejercicios)

### 3.1 — Tu agenda de hoy
```bash
gws calendar +agenda
```

### 3.2 — Standup report (reuniones + tareas)
```bash
gws workflow +standup-report
```

### 3.3 — Listar tus calendarios
```bash
gws calendar calendarList list
```

### 3.4 — Eventos de mañana
```bash
gws calendar events list --params '{"calendarId": "primary", "timeMin": "2026-03-09T00:00:00Z", "timeMax": "2026-03-09T23:59:59Z", "singleEvents": true, "orderBy": "startTime"}'
```

---

## 4. Sheets (4 ejercicios)

### 4.1 — Leer las cabeceras de una spreadsheet
Usa el id de cualquier spreadsheet que hayas encontrado en Drive.
```bash
gws sheets +read --spreadsheet '<ID>' --range 'A1:Z1'
```

### 4.2 — Leer un rango concreto
```bash
gws sheets +read --spreadsheet '<ID>' --range 'A1:E10'
```

### 4.3 — Ver la metadata de una spreadsheet
```bash
gws sheets spreadsheets get --params '{"spreadsheetId": "<ID>"}' | jq '{title: .properties.title, sheets: [.sheets[].properties.title]}'
```

### 4.4 — Añadir una fila de prueba (cuidado: esto escribe datos reales)
Solo si tienes una spreadsheet de prueba. Cambia el ID y el rango.
```bash
gws sheets +append --spreadsheet '<ID>' --range 'A1' --values '[["Test desde CLI", "2026-03-08", "Funciona"]]'
```

---

## 5. Docs (3 ejercicios)

### 5.1 — Ver la estructura de un documento
```bash
gws docs documents get --params '{"documentId": "<ID>"}' --format yaml | head -80
```

### 5.2 — Extraer solo el texto de un documento
```bash
gws docs documents get --params '{"documentId": "<ID>"}' | jq '[.body.content[].paragraph?.elements[]?.textRun?.content // empty] | join("")'
```

### 5.3 — Ver el título de un documento
```bash
gws docs documents get --params '{"documentId": "<ID>"}' | jq '.title'
```

---

## 6. Workflows (3 ejercicios)

### 6.1 — Standup completo
```bash
gws workflow +standup-report
```

### 6.2 — Preparar próxima reunión
```bash
gws workflow +meeting-prep
```

### 6.3 — Digest semanal
```bash
gws workflow +weekly-digest
```

---

## Consejo para trastear

- Usa `| jq .` para formatear el JSON bonito
- Usa `| jq '.clave'` para extraer campos específicos
- Usa `--format table` para ver datos en tabla cuando quieras algo visual
- Usa `--dry-run` si quieres ver qué haría un comando sin ejecutarlo
- Usa `gws schema <service.resource.method>` si quieres ver todos los parámetros disponibles
