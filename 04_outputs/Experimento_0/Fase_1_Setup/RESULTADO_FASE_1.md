# Resultado Fase 1 — Setup y Configuración

**Fecha:** 2026-03-08
**Estado:** Completada

---

## Resumen de pasos

| Paso | Estado | Fricción | Tiempo aprox. |
|---|---|---|---|
| 1. Verificar requisitos | OK | Ninguna | <1 min |
| 2. Instalar `gws` | OK | Ninguna | ~3 seg |
| 3. Proyecto OAuth (Cloud Console) | OK | Manual: crear proyecto, consent screen, credenciales, descargar JSON | ~10 min |
| 4. Auth login | OK | Bug menor: URL con encoding incorrecto en `prompt=select_account+consent` (Google lo toleró) | ~3 min |
| 5. Smoke test | Falló → OK | APIs no habilitadas por defecto en el proyecto GCP. Hay que activarlas manualmente. | ~5 min |
| 6. Exploración CLI | OK | Ninguna | ~5 min |

---

## Configuración resultante

- **gws versión:** 0.8.0
- **Node.js:** v22.22.0 / npm 11.10.0
- **Cuenta:** raul@reboot.academy
- **Credenciales:** `~/.config/gws/credentials.enc` (AES-256-GCM)
- **Client secret:** `~/.config/gws/client_secret.json`
- **Scopes autorizados:** Drive, Sheets, Gmail (modify), Calendar, Docs, Presentations, Tasks, OpenID, UserInfo

---

## Fricciones encontradas

### 1. Sin `gcloud` → setup manual obligatorio
Sin `gcloud` instalado, hay que crear manualmente el proyecto GCP, la pantalla de consentimiento OAuth, las credenciales de tipo Desktop, y descargar el JSON. No es difícil pero son varios pasos en la consola web.

### 2. Bug de encoding en URL de auth
`gws auth login` genera una URL con `prompt=select_account+consent` donde el `+` se interpreta como espacio. Google devuelve error 400. Sin embargo, al abrir la URL manualmente con `prompt=consent` (o simplemente reintentando), el flujo completó correctamente. Bug menor pero documentable.

### 3. APIs no habilitadas por defecto
El primer comando (`gws drive files list`) falló con 403 porque la Drive API no estaba habilitada en el proyecto GCP. Hay que ir a Cloud Console y activar cada API manualmente (Drive, Gmail, Sheets, Calendar, Docs, etc.). Fricción esperada pero no trivial para un usuario nuevo.

---

## Hallazgos del Paso 6 — Exploración del CLI

### Helpers (prefijo `+`)
Descubrimiento importante: `gws` incluye **helpers de alto nivel** que simplifican operaciones comunes. Son muy relevantes para uso agéntico:

| Servicio | Helpers disponibles |
|---|---|
| Drive | `+upload` — subir fichero con metadata automática |
| Gmail | `+send`, `+triage` (resumen inbox no leído), `+watch` (stream NDJSON) |
| Calendar | `+insert` (crear evento), `+agenda` (próximos eventos) |
| Sheets | `+append` (añadir fila), `+read` (leer valores) |
| Docs | `+write` (añadir texto) |

### Workflows cross-service
`gws workflow` ofrece flujos que combinan múltiples productos:

| Workflow | Descripción |
|---|---|
| `+standup-report` | Reuniones de hoy + tareas abiertas |
| `+meeting-prep` | Preparar próxima reunión: agenda, asistentes, docs |
| `+email-to-task` | Convertir email en tarea de Google Tasks |
| `+weekly-digest` | Resumen semanal: reuniones + emails no leídos |
| `+file-announce` | Anunciar fichero de Drive en Chat |

### Schema
`gws schema <service.resource.method>` devuelve el schema completo del método con todos los parámetros, tipos, descripciones y valores por defecto. Muy útil para explorar y para agentes.

### Superficie por producto

| Producto | Recursos principales |
|---|---|
| Drive | files, permissions, comments, replies, revisions, drives, changes |
| Gmail | users (mensajes, hilos, labels, drafts, settings, history) |
| Calendar | events, calendarList, acl, freebusy, settings |
| Sheets | spreadsheets (values, sheets, developerMetadata) |
| Docs | documents |

### Formatos de output
Soporta: `json` (default), `table`, `yaml`, `csv`. Buen rango para distintos usos.

---

## Valoración general del setup

| Dimensión | Valoración |
|---|---|
| Fricción de setup | **Media**. Sin `gcloud`, el setup OAuth es manual pero factible. Las APIs hay que activarlas una a una. |
| Usabilidad | **Alta**. CLI intuitivo, help claro, helpers muy bien pensados. |
| Agent-friendliness | **Alta**. JSON estructurado por defecto, schema disponible, NDJSON para paginación. |
| Fiabilidad | **Alta** hasta ahora. Un bug menor de encoding, todo lo demás funcionó a la primera. |
| Tiempo total de setup | **~25 minutos** de cero a primer comando exitoso. |

---

## Criterios de avance — verificación

- [x] `gws` instalado y funcional (v0.8.0)
- [x] Autenticación completada contra cuenta de Google Workspace (raul@reboot.academy)
- [x] Al menos un comando ejecutado con éxito (`gws drive files list`)
- [x] Documentada la fricción encontrada durante el setup

**Fase 1 cerrada. Lista para avanzar a Fase 2.**
