# Contrato de Eventos CRM-GWS — Fase 5 IA Mujeres 2026

**Fecha:** 2026-06-04
**Campaña:** SkilLand IA Mujeres 2026
**Business line:** SkilLand IA Mujeres
**Estado:** Diseño — pendiente integración con Twenty

---

## Eventos definidos

| Evento | Trigger | Quién lo genera |
|---|---|---|
| `draft_created` | CLI crea borrador en Gmail | GWS CLI |
| `email_sent` | Draft enviado o mensaje enviado directamente | GWS CLI |
| `reply_received` | Nuevo mensaje en un thread conocido | GWS CLI (polling o watch) |
| `send_failed` | Error en `users messages send` | GWS CLI |
| `bounce_detected` | Mensaje de sistema con Non-Delivery Report | GWS CLI (detección heurística) |
| `manual_review_required` | Flag por lógica de negocio | GWS CLI o agente supervisor |

---

## Schema de evento

```json
{
  "schema_version": "1.0",
  "campaign_name": "IA Mujeres 2026",
  "business_line": "SkilLand IA Mujeres",
  "event_type": "email_sent",
  "event_id": "<uuid-generado-por-el-cli>",
  "occurred_at": "2026-06-04T21:11:36Z",

  "sender_email": "gerencia@skilland.ai",
  "recipient_email": "contacto@organismo.es",
  "subject": "...",

  "message_id": "<gmail-message-id>",
  "thread_id": "<gmail-thread-id>",
  "draft_id": "<gmail-draft-id-si-aplica>",

  "crm_deal_id": "optional",
  "crm_person_id": "optional",
  "crm_company_id": "optional",

  "metadata": {
    "test_mode": false,
    "template_name": "primer_contacto_ia_mujeres",
    "sequence_step": 1,
    "sender_account_alias": "SENDER_ACCOUNT_1"
  }
}
```

---

## Campos por evento

### `draft_created`

| Campo | Fuente | Requerido |
|---|---|---|
| `event_type` | Literal | Sí |
| `sender_email` | `gws auth status` | Sí |
| `recipient_email` | Parámetro del draft | Sí |
| `subject` | Parámetro del draft | Sí |
| `draft_id` | Response de `drafts create` | Sí |
| `message_id` | Response `.message.id` | Sí |
| `thread_id` | Response `.message.threadId` | Sí |
| `occurred_at` | Timestamp local | Sí |
| `crm_deal_id` | CRM → GWS (input) | Opcional |
| `test_mode` | Config | Sí |

### `email_sent`

| Campo | Fuente | Requerido |
|---|---|---|
| `event_type` | Literal | Sí |
| `sender_email` | Header `From` del mensaje | Sí |
| `recipient_email` | Header `To` del mensaje | Sí |
| `subject` | Header `Subject` | Sí |
| `message_id` | Response de send o `messages get` | Sí |
| `thread_id` | Response `.threadId` | Sí |
| `sent_at` | Header `Date` del mensaje | Sí |
| `crm_deal_id` | CRM → GWS (input) | Opcional |
| `crm_person_id` | CRM → GWS (input) | Opcional |

### `reply_received`

| Campo | Fuente | Requerido |
|---|---|---|
| `event_type` | Literal | Sí |
| `reply_message_id` | `messages get .id` del reply | Sí |
| `thread_id` | Compartido con `email_sent` | Sí |
| `from` | Header `From` del reply | Sí |
| `to` | Header `To` del reply | Sí |
| `subject` | Header `Subject` | Sí |
| `received_at` | Header `Date` | Sí |
| `snippet` | `.snippet` del mensaje | Opcional |
| `original_message_id` | `In-Reply-To` header | Opcional |
| `crm_deal_id` | Lookup por thread_id en CRM | Opcional |

### `send_failed`

| Campo | Fuente | Requerido |
|---|---|---|
| `event_type` | Literal | Sí |
| `sender_email` | Cuenta activa | Sí |
| `recipient_email` | Intento de envío | Sí |
| `error_code` | HTTP status o mensaje de error | Sí |
| `error_message` | Texto del error | Sí |
| `occurred_at` | Timestamp local | Sí |

### `bounce_detected`

| Campo | Fuente | Requerido |
|---|---|---|
| `event_type` | Literal | Sí |
| `original_message_id` | Header del bounce | Sí |
| `bounced_recipient` | Extraído del body del bounce | Sí |
| `bounce_message_id` | `messages get .id` del NDR | Sí |
| `received_at` | Fecha del NDR | Sí |

---

## Ejemplos JSON completos

### Ejemplo: `email_sent` en modo test

```json
{
  "schema_version": "1.0",
  "campaign_name": "IA Mujeres 2026",
  "business_line": "SkilLand IA Mujeres",
  "event_type": "email_sent",
  "event_id": "evt_20260604_001",
  "occurred_at": "2026-06-04T21:11:36Z",
  "sender_email": "sales@reboot.academy",
  "recipient_email": "sales@reboot.academy",
  "subject": "[TEST IA Mujeres] Validacion draft GWS CLI",
  "message_id": "19e947a32828729b",
  "thread_id": "19e9479fdb5105cb",
  "draft_id": "r-8851880231221819129",
  "crm_deal_id": null,
  "crm_person_id": null,
  "crm_company_id": null,
  "metadata": {
    "test_mode": true,
    "template_name": "smoke_test",
    "sequence_step": 1,
    "sender_account_alias": "CONTROL_ACCOUNT"
  }
}
```

### Ejemplo: `reply_received`

```json
{
  "schema_version": "1.0",
  "campaign_name": "IA Mujeres 2026",
  "business_line": "SkilLand IA Mujeres",
  "event_type": "reply_received",
  "event_id": "evt_20260604_002",
  "occurred_at": "2026-06-04T22:00:00Z",
  "reply_message_id": "<reply-message-id>",
  "thread_id": "19e9479fdb5105cb",
  "from": "sales@reboot.academy",
  "to": "sales@reboot.academy",
  "subject": "Re: [TEST IA Mujeres] Validacion draft GWS CLI",
  "received_at": "2026-06-04T22:00:00Z",
  "snippet": "Recibido test GWS CLI. Podemos continuar.",
  "original_message_id": "19e947a32828729b",
  "crm_deal_id": null,
  "metadata": {
    "test_mode": true,
    "auto_detected": true
  }
}
```

### Ejemplo: `email_sent` real (campaña activa)

```json
{
  "schema_version": "1.0",
  "campaign_name": "IA Mujeres 2026",
  "business_line": "SkilLand IA Mujeres",
  "event_type": "email_sent",
  "event_id": "evt_20260615_0034",
  "occurred_at": "2026-06-15T09:15:00Z",
  "sender_email": "gerencia@skilland.ai",
  "recipient_email": "contacto@organismo.es",
  "subject": "SkilLand IA Mujeres — Programa de formación certificada",
  "message_id": "<gmail-message-id>",
  "thread_id": "<gmail-thread-id>",
  "draft_id": "<draft-id>",
  "crm_deal_id": "deal_twenty_abc123",
  "crm_person_id": "person_twenty_xyz456",
  "crm_company_id": "company_twenty_def789",
  "metadata": {
    "test_mode": false,
    "template_name": "primer_contacto_v1",
    "sequence_step": 1,
    "sender_account_alias": "SENDER_ACCOUNT_1"
  }
}
```

---

## Qué aporta cada sistema

| Campo | Lo aporta GWS | Lo aporta CRM (Twenty) | Compartido |
|---|---|---|---|
| `message_id` | Sí | — | — |
| `thread_id` | Sí | — | — |
| `draft_id` | Sí | — | — |
| `sender_email` | Sí | — | — |
| `recipient_email` | Sí | Sí (como fuente) | — |
| `subject` | Sí | Sí (como fuente) | — |
| `sent_at` | Sí | — | — |
| `crm_deal_id` | — | Sí | — |
| `crm_person_id` | — | Sí | — |
| `crm_company_id` | — | Sí | — |
| `campaign_name` | Config | — | — |
| `test_mode` | Config | — | — |

---

## Qué queda pendiente para integración

| Item | Prioridad | Notas |
|---|---|---|
| Endpoint en Twenty para ingerir eventos | Alta | API REST o webhook |
| Lookup `thread_id → crm_deal_id` | Alta | La asociación debe existir antes del envío |
| Polling de replies (¿cada cuánto?) | Media | `gws gmail +watch` o cron |
| Detección heurística de bounces | Media | Buscar `MAILER-DAEMON` en INBOX |
| Schema validation del evento antes de enviar | Media | JSON Schema o Zod |
| `event_id` generation strategy | Baja | UUID v4 o timestamp+hash |
| Retries para `send_failed` | Baja | Solo con supervisión humana inicialmente |
