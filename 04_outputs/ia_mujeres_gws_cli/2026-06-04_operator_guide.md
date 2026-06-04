# Guía Operativa GWS CLI — Campaña IA Mujeres 2026

**Fecha:** 2026-06-04
**Versión:** 1.0
**Para:** Operador de campaña

---

## Regla de oro

**Antes de cualquier operación de envío:**
```bash
gws auth status | grep '"user"'
```
Verificar que la cuenta que aparece es la que quieres usar. Si no coincide, usar el alias o la variable de entorno correcta.

---

## Selección de cuenta emisora

| Cuenta | Comando |
|---|---|
| `sales@reboot.academy` (CONTROL) | `gws [comando]` |
| `gerencia@skilland.ai` (SENDER_1) | `GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia gws [comando]` |
| `direccion@skilland.ai` (SENDER_2) | `GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_direccion gws [comando]` |

**Con aliases (si los tienes en `.zshrc`):**
```bash
gws-sales [comando]
gws-gerencia [comando]
gws-direccion [comando]
```

---

## 1. Verificar autenticación

```bash
# Cuenta principal
gws auth status

# Cuenta gerencia
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia gws auth status

# Cuenta direccion
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_direccion gws auth status
```

Campos clave a verificar: `"user"` (email correcto) y `"token_valid": true`.

---

## 2. Crear un draft

### Paso 1: Codificar el mensaje

```python
import base64

msg = """From: gerencia@skilland.ai
To: destinatario@organismo.es
Subject: SkilLand IA Mujeres — Formación certificada
Content-Type: text/plain; charset=UTF-8
MIME-Version: 1.0

Cuerpo del mensaje aquí...
"""

encoded = base64.urlsafe_b64encode(msg.encode()).decode().rstrip('=')
print(encoded)
```

### Paso 2: Crear el draft

```bash
ENCODED="<output del paso anterior>"

GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws gmail users drafts create \
  --params '{"userId":"me"}' \
  --json "{\"message\":{\"raw\":\"$ENCODED\"}}"
```

**Guardar los IDs del output:**
```json
{
  "id": "<draft_id>",
  "message": {
    "id": "<message_id>",
    "threadId": "<thread_id>"
  }
}
```

### Paso 3: Verificar el draft antes de enviar

```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws gmail users drafts get \
  --params '{"userId":"me","id":"<draft_id>","format":"metadata"}'
```

---

## 3. Enviar un email test

**IMPORTANTE:** Solo enviar a cuentas que controlas. Nunca a contactos reales en esta fase.

```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws gmail users drafts send \
  --params '{"userId":"me"}' \
  --json '{"id":"<draft_id>"}'
```

**Output esperado:**
```json
{
  "id": "<message_id_enviado>",
  "labelIds": ["SENT"],
  "threadId": "<thread_id>"
}
```

---

## 4. Buscar un thread o email

### Por asunto (subject)
```bash
gws gmail users messages list \
  --params '{"userId":"me","q":"subject:[TEST IA Mujeres]","maxResults":10}'
```

### Por remitente
```bash
gws gmail users messages list \
  --params '{"userId":"me","q":"from:gerencia@skilland.ai","maxResults":10}'
```

### Por thread_id
```bash
gws gmail users messages list \
  --params '{"userId":"me","q":"thread:<thread_id>"}'
```

### Por fecha
```bash
gws gmail users messages list \
  --params '{"userId":"me","q":"after:2026/06/04 subject:[TEST IA Mujeres]"}'
```

---

## 5. Leer metadata de un mensaje

```bash
gws gmail users messages get \
  --params '{"userId":"me","id":"<message_id>","format":"metadata"}' | \
  python3 -c "
import json,sys
d=json.load(sys.stdin)
h={x['name']:x['value'] for x in d['payload']['headers']}
print('message_id:', d['id'])
print('thread_id:', d['threadId'])
print('From:', h.get('From'))
print('To:', h.get('To'))
print('Subject:', h.get('Subject'))
print('Date:', h.get('Date'))
print('Labels:', d['labelIds'])
print('Snippet:', d.get('snippet','')[:100])
"
```

---

## 6. Detectar una respuesta

### Opción A: Leer el hilo completo
```bash
gws gmail users threads get \
  --params '{"userId":"me","id":"<thread_id>","format":"metadata"}' | \
  python3 -c "
import json,sys
d=json.load(sys.stdin)
msgs=d.get('messages',[])
print(f'Mensajes en hilo: {len(msgs)}')
for m in msgs:
    h={x[\"name\"]:x[\"value\"] for x in m[\"payload\"][\"headers\"]}
    print(f'  [{m[\"id\"]}] From:{h.get(\"From\")} Date:{h.get(\"Date\")}')
"
```

Si `len(msgs) > 1`, hay respuesta.

### Opción B: Buscar replies por hilo
```bash
gws gmail users messages list \
  --params '{"userId":"me","q":"thread:<thread_id>"}'
```

### Opción C: Stream de nuevos emails (monitoreo activo)
```bash
gws gmail +watch
```
Emite NDJSON con cada nuevo email recibido. Útil para detección en tiempo real.

---

## 7. Exportar evento para CRM

Después de un envío exitoso, generar el evento en el formato del contrato:

```python
import json, datetime

event = {
    "schema_version": "1.0",
    "campaign_name": "IA Mujeres 2026",
    "business_line": "SkilLand IA Mujeres",
    "event_type": "email_sent",
    "event_id": f"evt_{datetime.datetime.now().strftime('%Y%m%d_%H%M%S')}",
    "occurred_at": datetime.datetime.utcnow().isoformat() + "Z",
    "sender_email": "gerencia@skilland.ai",
    "recipient_email": "destinatario@organismo.es",
    "subject": "...",
    "message_id": "<de la respuesta de send>",
    "thread_id": "<de la respuesta de send>",
    "draft_id": "<del draft creado>",
    "crm_deal_id": None,
    "crm_person_id": None,
    "crm_company_id": None,
    "metadata": {
        "test_mode": False,
        "template_name": "primer_contacto_v1",
        "sequence_step": 1,
        "sender_account_alias": "SENDER_ACCOUNT_1"
    }
}

print(json.dumps(event, indent=2, ensure_ascii=False))
```

Guardar en un archivo NDJSON para batch import a Twenty cuando esté listo:
```bash
python3 generate_event.py >> 04_outputs/ia_mujeres_gws_cli/events.ndjson
```

---

## 8. Evitar enviar a contactos reales por error

### Lista blanca de destinatarios seguros para tests

```bash
ALLOWED_RECIPIENTS=(
  "sales@reboot.academy"
  "gerencia@skilland.ai"
  "direccion@skilland.ai"
)
```

### Validación antes de enviar (script Python)

```python
ALLOWED = {
    "sales@reboot.academy",
    "gerencia@skilland.ai",
    "direccion@skilland.ai",
}

def safe_to_send(recipient: str) -> bool:
    if recipient.lower() not in ALLOWED:
        raise ValueError(
            f"ABORT: {recipient} no está en la lista blanca de destinatarios seguros. "
            "Añade explícitamente a ALLOWED si es una cuenta controlada."
        )
    return True
```

### Reglas operativas

1. **Fase de test:** Todos los `To:` deben ser cuentas propias.
2. **Fase de campaña real:** Verificar que `test_mode: false` está explícito y el deal existe en CRM.
3. **Nunca** enviar desde scripts sin revisión humana del draft primero.
4. **Siempre** crear draft → revisar → enviar. No enviar directamente.

---

## 9. Etiquetado de emails de campaña

Crear una etiqueta para la campaña (una vez):
```bash
gws gmail users labels create \
  --params '{"userId":"me"}' \
  --json '{"name":"IA Mujeres 2026","labelListVisibility":"labelShow","messageListVisibility":"show"}'
```

Aplicar etiqueta a un mensaje:
```bash
gws gmail users messages modify \
  --params '{"userId":"me","id":"<message_id>"}' \
  --json '{"addLabelIds":["<label_id>"]}'
```

---

## Referencia rápida de comandos

| Acción | Comando |
|---|---|
| Ver cuenta activa | `gws auth status` |
| Inbox no leído | `gws gmail +triage` |
| Crear draft | `gws gmail users drafts create ...` |
| Enviar draft | `gws gmail users drafts send ...` |
| Listar enviados | `gws gmail users messages list --params '{"userId":"me","q":"in:sent"}'` |
| Leer mensaje | `gws gmail users messages get --params '{"userId":"me","id":"..."}'` |
| Leer hilo | `gws gmail users threads get --params '{"userId":"me","id":"..."}'` |
| Buscar por query | `gws gmail users messages list --params '{"userId":"me","q":"..."}'` |
| Ver schema de método | `gws schema gmail.users.messages.send` |
| Stream nuevos emails | `gws gmail +watch` |
