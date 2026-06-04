# Auditoría GWS CLI — Fase 5 IA Mujeres 2026

**Fecha:** 2026-06-04
**Auditor:** Claude Code (Fase 5 automatización)
**Estado:** Completada

---

## Versión y entorno

| Item | Valor |
|---|---|
| `gws` versión | 0.8.0 |
| Node.js | v22.22.0 / npm 11.10.0 |
| Shell | zsh |
| Config dir default | `~/.config/gws/` |
| GCP proyecto | `gws-cli-experiment-raul` |

---

## Cuenta activa (CONTROL_ACCOUNT)

| Campo | Valor |
|---|---|
| Email | `sales@reboot.academy` |
| Auth method | OAuth2 (Desktop app) |
| Credenciales | `~/.config/gws/credentials.enc` (AES-256-GCM) |
| Client secret | `~/.config/gws/client_secret.json` |
| Encryption key | `~/.config/gws/.encryption_key` |
| Token | Válido (verificado 2026-06-04) |
| Scopes count | 11 |

---

## Scopes autorizados

| Scope | Propósito |
|---|---|
| `email` | Leer dirección de email |
| `openid` | OpenID Connect |
| `https://www.googleapis.com/auth/userinfo.email` | Identificar cuenta |
| `https://www.googleapis.com/auth/gmail.modify` | Leer/enviar/etiquetar Gmail |
| `https://www.googleapis.com/auth/drive` | Drive completo |
| `https://www.googleapis.com/auth/calendar` | Calendar completo |
| `https://www.googleapis.com/auth/documents` | Google Docs |
| `https://www.googleapis.com/auth/spreadsheets` | Sheets |
| `https://www.googleapis.com/auth/presentations` | Slides |
| `https://www.googleapis.com/auth/tasks` | Google Tasks |
| `https://www.googleapis.com/auth/contacts.readonly` | Contactos (solo lectura) |

**Nota:** El scope `gmail.modify` cubre: leer, enviar, crear drafts, etiquetar, mover. Es el scope correcto para operaciones de campaña.

---

## Comandos disponibles

### Servicios

| Servicio | Descripción |
|---|---|
| `drive` | Archivos, carpetas, shared drives |
| `sheets` | Hojas de cálculo |
| `gmail` | Email completo |
| `calendar` | Calendarios y eventos |
| `admin-reports` | Logs de auditoría |
| `docs` | Google Docs |
| `slides` | Presentaciones |
| `tasks` | Google Tasks |
| `people` | Contactos |
| `chat` | Google Chat |
| `classroom` | Google Classroom |
| `forms` | Google Forms |
| `keep` | Google Keep |
| `meet` | Google Meet |
| `workflow` | Workflows cross-service |

### Helpers Gmail relevantes para campaña

| Comando | Descripción |
|---|---|
| `gws gmail +send` | Enviar email (texto plano) |
| `gws gmail +triage` | Resumen inbox no leído |
| `gws gmail +watch` | Stream NDJSON de nuevos emails |

### Comandos Gmail API relevantes

| Comando | Descripción |
|---|---|
| `gws gmail users messages list` | Listar mensajes (devuelve solo IDs) |
| `gws gmail users messages get` | Leer mensaje completo |
| `gws gmail users messages send` | Enviar mensaje (MIME raw) |
| `gws gmail users drafts create` | Crear borrador |
| `gws gmail users drafts get` | Leer borrador |
| `gws gmail users drafts list` | Listar borradores |
| `gws gmail users drafts send` | Enviar borrador existente |
| `gws gmail users drafts update` | Actualizar borrador |
| `gws gmail users threads get` | Leer hilo completo |
| `gws gmail users threads list` | Listar hilos |
| `gws gmail users labels list` | Listar etiquetas |
| `gws gmail users labels create` | Crear etiqueta |
| `gws gmail users history list` | Historial de cambios |

---

## Modelo de autenticación

```
GCP Project (gws-cli-experiment-raul)
  └── OAuth2 Desktop App
        └── client_secret.json  ← credencial del app (no personal)
              └── gws auth login  ← flujo PKCE en navegador
                    └── credentials.enc  ← token de la cuenta (AES-256-GCM)
                          └── .encryption_key  ← clave AES local
```

**Multi-account:** No hay flag `--account`. El mecanismo oficial es `GOOGLE_WORKSPACE_CLI_CONFIG_DIR` para apuntar a directorios separados, cada uno con sus propias credenciales.

**Refresh token:** El CLI gestiona el refresh automáticamente. No se rompe el flujo salvo revocación manual del token.

---

## Soporte multi-account

| Característica | Soporte | Mecanismo |
|---|---|---|
| Múltiples cuentas | Sí (indirecto) | `GOOGLE_WORKSPACE_CLI_CONFIG_DIR` |
| Cambio de cuenta en caliente | No nativo | Requiere cambiar var de entorno |
| Wrapper/alias por cuenta | Posible | Script shell con la var seteada |
| Scopes por cuenta | Independientes | Cada dir tiene su propio token |
| Mezcla accidental de cuentas | Riesgo bajo | La var está explícita en cada llamada |

---

## Capacidades verificadas (smoke test 2026-06-04)

| Capacidad | Estado | Notas |
|---|---|---|
| Auth status | OK | `sales@reboot.academy`, token válido |
| Listar inbox (`+triage`) | OK | 8 emails no leídos visibles |
| Crear draft | OK | `draft_id: r-8851880231221819129` |
| Enviar draft | OK | `message_id: 19e947a32828729b` |
| Leer mensaje enviado | OK | Metadata completa extraída |
| Extraer `message_id` | OK | `19e947a32828729b` |
| Extraer `thread_id` | OK | `19e9479fdb5105cb` |
| Buscar por subject | OK | Query `subject:[TEST IA Mujeres]` funciona |
| Leer hilo por `thread_id` | OK | Thread con 1 mensaje confirmado |

---

## Ubicación segura de credenciales

| Archivo | Ubicación | Sensible | Versionable |
|---|---|---|---|
| `client_secret.json` | `~/.config/gws/` | Sí (secret del app) | NO |
| `credentials.enc` | `~/.config/gws/` | Sí (token cifrado) | NO |
| `.encryption_key` | `~/.config/gws/` | Sí (clave AES) | NO |
| `token_cache.json` | `~/.config/gws/` | Sí (tokens en caché) | NO |
| `.env` (si se crea) | raíz del proyecto | Sí | NO (solo `.env.example`) |

**Regla:** Todo lo que esté en `~/.config/gws*/` nunca va al repo. Ver `.gitignore`.

---

## Tracking de aperturas

| Evento | Disponible vía Gmail API | Notas |
|---|---|---|
| `email_sent` | Sí | Label `SENT` en el mensaje |
| `email_delivered` | No | Gmail no expone delivery API |
| `email_opened` | No | Gmail no expone apertura |
| `reply_received` | Sí | Buscar por `threadId` o `in-reply-to` |
| `bounce` | Parcial | Aparece como mensaje de sistema en INBOX |
| `link_click` | No | Requiere pixel/redirect externo |

**Conclusión:** Para esta campaña, el tracking fiable es:
- `draft_created`, `email_sent`, `reply_received`, `bounce_detected_if_available`

No inventar aperturas. Si se necesita tracking de apertura en el futuro, implementar pixel de tracking externo.

---

## Riesgos identificados

| Riesgo | Severidad | Mitigación |
|---|---|---|
| Token revocado en `gerencia/direccion` sin saberlo | Media | `gws auth status` antes de cada flujo automático |
| Envío accidental a contacto real | Alta | Validar `To:` contra lista blanca antes de enviar |
| `client_secret.json` commiteado al repo | Alta | `.gitignore` activo, ver reglas |
| Mezcla de cuentas por var de entorno mal seteada | Media | Verificar cuenta activa antes de cada draft/send |
| OAuth consent screen solo permite usuarios de test | Media | Añadir `gerencia@skilland.ai` y `direccion@skilland.ai` como test users en GCP |
| Dominio `skilland.ai` no autorizado en OAuth app | Alta | Ver sección multi-account en plan de setup |

---

## Gaps detectados

| Gap | Impacto | Solución |
|---|---|---|
| `gerencia@skilland.ai` aún no tiene credenciales | Bloqueante para SENDER_ACCOUNT_1 | Auth manual requerida (ver guía) |
| `direccion@skilland.ai` aún no tiene credenciales | Bloqueante para SENDER_ACCOUNT_2 | Auth manual requerida |
| No hay `.gitignore` en el repo | Riesgo de credenciales expuestas | Creado en esta fase |
| El helper `+send` solo soporta texto plano | Limita emails HTML | Usar `users messages send` con MIME raw |
| No hay lista blanca de destinatarios seguros | Riesgo operativo | Implementar validación antes de send |
| OAuth app en modo "testing" | Limita a usuarios aprobados | Añadir emails de Skilland.ai como test users |
