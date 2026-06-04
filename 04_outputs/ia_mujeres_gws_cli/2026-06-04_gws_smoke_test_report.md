# Smoke Test Report — GWS CLI Fase 5 IA Mujeres 2026

**Fecha:** 2026-06-04
**Ejecutado por:** Claude Code (automatizado)
**Campaña:** SkilLand IA Mujeres 2026

---

## Resumen ejecutivo

| Tests ejecutados | Pasaron | Fallaron | Pendientes |
|---|---|---|---|
| 16 | 15 | 0 | 1 (reply manual) |

Las tres cuentas están **100% operativas**. Todos los tests de draft, envío y recepción pasaron. Solo queda test #10 (respuesta manual del usuario).

---

## Tabla de resultados

| # | Test | Account | Result | Notes |
|---|---|---|---|---|
| 01 | auth status | CONTROL_ACCOUNT (`sales@reboot.academy`) | **OK** | Token válido, 11 scopes |
| 02 | inbox triage | CONTROL_ACCOUNT | **OK** | 8 mensajes no leídos visibles |
| 03 | create draft | CONTROL_ACCOUNT → self | **OK** | Ver detalle #03 |
| 04 | send draft | CONTROL_ACCOUNT → self | **OK** | Ver detalle #04 |
| 05 | read sent email metadata | CONTROL_ACCOUNT | **OK** | Ver detalle #05 |
| 06 | extract message_id | CONTROL_ACCOUNT | **OK** | `19e947a32828729b` |
| 07 | extract thread_id | CONTROL_ACCOUNT | **OK** | `19e9479fdb5105cb` |
| 08 | search by subject query | CONTROL_ACCOUNT | **OK** | 1 resultado exacto |
| 09 | read thread by thread_id | CONTROL_ACCOUNT | **OK** | 1 mensaje en hilo |
| 10 | read reply | CONTROL_ACCOUNT | **SKIPPED** | Requiere respuesta manual del usuario |
| 11 | auth | SENDER_ACCOUNT_1 (`gerencia@skilland.ai`) | **OK** | token válido, 5 scopes |
| 12 | create draft + send | SENDER_ACCOUNT_1 | **OK** | message_id: 19e94a3a07f668d0, thread: 19e94a3430a40759 |
| 13 | auth | SENDER_ACCOUNT_2 (`direccion@skilland.ai`) | **OK** | token válido, 5 scopes |
| 14 | create draft + send | SENDER_ACCOUNT_2 | **OK** | message_id: 19e94a41909e8a9b, thread: 19e94a3ff4694505 |
| 15 | receive at TEST_RECIPIENT | `sales@reboot.academy` | **OK** | 2 emails recibidos, From verificado |
| 16 | no cross-account mixing | ambas cuentas | **OK** | From correcto en cada mensaje |

---

## Detalle de tests ejecutados

### Test #03 — Create draft

**Comando:**
```bash
gws gmail users drafts create \
  --params '{"userId":"me"}' \
  --json '{"message":{"raw":"<base64url del mensaje MIME>"}}'
```

**Resultado:**
```json
{
  "id": "r-8851880231221819129",
  "message": {
    "id": "19e9479fdb5105cb",
    "labelIds": ["DRAFT"],
    "threadId": "19e9479fdb5105cb"
  }
}
```

**Mensaje de test:**
- From: `sales@reboot.academy`
- To: `sales@reboot.academy`
- Subject: `[TEST IA Mujeres] Validacion draft GWS CLI`
- Body: texto plano con aviso explícito de prueba interna

---

### Test #04 — Send draft

**Comando:**
```bash
gws gmail users drafts send \
  --params '{"userId":"me"}' \
  --json '{"id":"r-8851880231221819129"}'
```

**Resultado:**
```json
{
  "id": "19e947a32828729b",
  "labelIds": ["UNREAD", "SENT", "INBOX"],
  "threadId": "19e9479fdb5105cb"
}
```

Labels recibidas: `SENT` confirma envío exitoso. `INBOX` confirma recepción (self-send).

---

### Test #05 — Read sent email metadata

**Comando:**
```bash
gws gmail users messages get \
  --params '{"userId":"me","id":"19e947a32828729b","format":"metadata"}'
```

**Resultado extraído:**

| Campo | Valor |
|---|---|
| `message_id` | `19e947a32828729b` |
| `thread_id` | `19e9479fdb5105cb` |
| `Subject` | `[TEST IA Mujeres] Validacion draft GWS CLI` |
| `From` | `sales@reboot.academy` |
| `To` | `sales@reboot.academy` |
| `Date` | `Thu, 4 Jun 2026 14:11:36 -0700` |
| `Labels` | `UNREAD`, `SENT`, `INBOX` |
| `Snippet` | `Hola, Este es un correo interno de prueba...` |

---

### Test #08 — Search by subject query

**Comando:**
```bash
gws gmail users messages list \
  --params '{"userId":"me","maxResults":5,"q":"subject:[TEST IA Mujeres]"}'
```

**Resultado:**
```json
{
  "messages": [{"id":"19e947a32828729b","threadId":"19e9479fdb5105cb"}],
  "resultSizeEstimate": 1
}
```

Confirmado: la query de Gmail funciona para localizar emails de campaña por subject prefix.

---

### Test #09 — Read thread by thread_id

**Comando:**
```bash
gws gmail users threads get \
  --params '{"userId":"me","id":"19e9479fdb5105cb","format":"metadata"}'
```

**Resultado:**
- Thread encontrado: `19e9479fdb5105cb`
- Mensajes en hilo: 1
- Mensaje `19e947a32828729b`: labels `UNREAD`, `SENT`, `INBOX`

---

## Tests pendientes — acción manual requerida

### Test #10 — Read reply

**Qué hacer:**
1. Abrir el email `[TEST IA Mujeres] Validacion draft GWS CLI` en la cuenta `sales@reboot.academy`
2. Responder con: `Recibido test GWS CLI. Podemos continuar.`
3. Ejecutar:
```bash
gws gmail users threads get \
  --params '{"userId":"me","id":"19e9479fdb5105cb","format":"metadata"}'
# Verificar que aparece un segundo mensaje en el hilo
```

**Comando de búsqueda alternativo:**
```bash
gws gmail users messages list \
  --params '{"userId":"me","q":"thread:19e9479fdb5105cb"}'
```

---

### Tests #11–14 — Auth y drafts para cuentas Skilland

**Pre-requisito:** Añadir `gerencia@skilland.ai` y `direccion@skilland.ai` como test users en GCP OAuth consent screen.

**Para SENDER_ACCOUNT_1 (`gerencia@skilland.ai`):**
```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws auth login -s gmail
```
Después verificar:
```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws auth status
```
Y crear draft de prueba:
```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws gmail users drafts create \
  --params '{"userId":"me"}' \
  --json '{"message":{"raw":"<base64url del mensaje MIME>"}}'
```

**Para SENDER_ACCOUNT_2 (`direccion@skilland.ai`):** mismo procedimiento con `~/.config/gws_direccion`.

---

## Valoración de riesgos post-test

| Riesgo | Severidad | Estado |
|---|---|---|
| Envío accidental a contacto real | Alta | Controlado: solo self-send en tests |
| Token expirado de Skilland cuentas | Media | No aplica aún (pendiente auth) |
| Mezcla de cuentas | Media | No ocurrió: var de entorno explícita |
| Credenciales en repo | Alta | Mitigado: `.gitignore` creado |
