# Plan de Configuración Multi-Account — Fase 5 IA Mujeres 2026

**Fecha:** 2026-06-04
**Estado:** CONTROL_ACCOUNT activo · SENDER cuentas pendientes de auth manual

---

## Mapa de cuentas

| Alias | Email | Estado | Config dir |
|---|---|---|---|
| CONTROL_ACCOUNT | `sales@reboot.academy` | Activo, token válido | `~/.config/gws/` |
| SENDER_ACCOUNT_1 | `gerencia@skilland.ai` | Dir creado, auth pendiente | `~/.config/gws_gerencia/` |
| SENDER_ACCOUNT_2 | `direccion@skilland.ai` | Dir creado, auth pendiente | `~/.config/gws_direccion/` |
| TEST_RECIPIENT | `sales@reboot.academy` | Misma que CONTROL | (ya activo) |

---

## Arquitectura de multi-account

```
~/.config/
  gws/                        ← CONTROL_ACCOUNT (sales@reboot.academy)
    client_secret.json        ← OAuth app compartido (gws-cli-experiment-raul)
    credentials.enc           ← token de sales@reboot.academy
    .encryption_key           ← clave AES
    token_cache.json

  gws_gerencia/               ← SENDER_ACCOUNT_1 (gerencia@skilland.ai)
    client_secret.json        ← mismo OAuth app (copiado)
    credentials.enc           ← pendiente: auth manual
    .encryption_key           ← se genera con auth login
    token_cache.json          ← se genera con auth login

  gws_direccion/              ← SENDER_ACCOUNT_2 (direccion@skilland.ai)
    client_secret.json        ← mismo OAuth app (copiado)
    credentials.enc           ← pendiente: auth manual
    .encryption_key           ← se genera con auth login
    token_cache.json          ← se genera con auth login
```

**Reutilizable:** El OAuth app (`client_secret.json`) es el mismo para todas las cuentas. No hay que crear un proyecto GCP nuevo.

**Por cuenta:** Solo el token (`credentials.enc`) y la clave AES (`.encryption_key`) son específicos de cada cuenta.

---

## Pre-requisito crítico: Test users en GCP

Como el OAuth app está en modo "testing" (no publicado), solo usuarios explícitamente aprobados pueden autenticarse.

**Acción requerida antes de hacer auth login para cuentas Skilland:**

1. Ir a [Google Cloud Console](https://console.cloud.google.com/)
2. Proyecto: `gws-cli-experiment-raul`
3. Menú: APIs & Services → OAuth consent screen
4. Sección "Test users" → Add users
5. Añadir:
   - `gerencia@skilland.ai`
   - `direccion@skilland.ai`
6. Guardar

Sin este paso, el flujo OAuth rechaza con error 403 "access_denied" para esas cuentas.

---

## Scopes a solicitar para SENDER cuentas

Los SENDER_ACCOUNTs solo necesitan operar Gmail para la campaña. Scopes mínimos:

```
gmail.modify          ← leer + enviar + drafts + labels
userinfo.email        ← identificar cuenta activa
email                 ← email address
openid               ← OAuth standard
```

Comando de auth con scopes limitados a Gmail:

```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws auth login -s gmail
```

Si en el futuro se necesita Drive o Calendar, re-autenticar con scopes adicionales.

---

## Procedimiento de auth por cuenta

### CONTROL_ACCOUNT — ya activo

```bash
gws auth status
# Debe mostrar: user: sales@reboot.academy, token_valid: true
```

### SENDER_ACCOUNT_1 (gerencia@skilland.ai)

**Paso 1:** Verificar que el dir está listo:
```bash
ls ~/.config/gws_gerencia/
# Debe mostrar: client_secret.json
```

**Paso 2:** Lanzar auth login (requiere navegador):
```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws auth login -s gmail
```
El CLI abre una URL en el navegador. Iniciar sesión con `gerencia@skilland.ai`. Aceptar los scopes.

**Paso 3:** Verificar:
```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia \
  gws auth status
# Debe mostrar: user: gerencia@skilland.ai, token_valid: true
```

### SENDER_ACCOUNT_2 (direccion@skilland.ai)

Mismo procedimiento con `gws_direccion`:
```bash
GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_direccion \
  gws auth login -s gmail

GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_direccion \
  gws auth status
```

---

## Aliases de shell recomendados

Añadir a `~/.zshrc` o `~/.bashrc`:

```bash
# GWS CLI — aliases por cuenta
alias gws-sales='gws'
alias gws-gerencia='GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia gws'
alias gws-direccion='GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_direccion gws'

# Verificación rápida de cuenta activa
alias gws-whoami='gws auth status | grep user'
alias gws-whoami-gerencia='GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia gws auth status | grep user'
alias gws-whoami-direccion='GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_direccion gws auth status | grep user'
```

---

## Permisos por cuenta

| Cuenta | Scopes | Token | Notas |
|---|---|---|---|
| `sales@reboot.academy` | 11 | Válido | Drive, Gmail, Calendar, Docs, Sheets, Slides, Tasks, Contacts, OpenID |
| `gerencia@skilland.ai` | 12 | Válido | Igual que sales + pubsub (autenticado 2026-06-04) |
| `direccion@skilland.ai` | 12 | Válido | Igual que sales + pubsub (autenticado 2026-06-04) |

---

## Qué NO versionar

Todos estos paths deben estar en `.gitignore`:

```
# Credenciales GWS - nunca al repo
~/.config/gws/
~/.config/gws_gerencia/
~/.config/gws_direccion/

# En el repo local del proyecto:
.env
*.enc
.encryption_key
token_cache.json
client_secret.json
credentials.json
```

**El `.gitignore` del repo ya incluye estas reglas** (creado en esta fase).

---

## Diagrama de flujo de selección de cuenta

```
¿Qué cuenta necesito usar?
    │
    ├─ sales@reboot.academy  → gws [comando]
    │   (o gws-sales alias)
    │
    ├─ gerencia@skilland.ai  → GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_gerencia gws [comando]
    │   (o gws-gerencia alias)
    │
    └─ direccion@skilland.ai → GOOGLE_WORKSPACE_CLI_CONFIG_DIR=~/.config/gws_direccion gws [comando]
        (o gws-direccion alias)
```

---

## Validación de que no se mezclan cuentas

Antes de cualquier draft o envío, verificar la cuenta activa:

```bash
# Verificar ANTES de crear draft
gws auth status | grep '"user"'
# Output esperado: "user": "gerencia@skilland.ai"

# Si la cuenta es incorrecta: DETENER y setear la var correcta
```

Este check debe ser el primer paso de cualquier script automatizado.

---

## Estado actual (2026-06-04)

| Acción | Estado |
|---|---|
| Config dirs creados (`gws_gerencia`, `gws_direccion`) | Hecho |
| `client_secret.json` copiado a cada dir | Hecho |
| Test users añadidos en GCP para `skilland.ai` | Pendiente — acción manual |
| Auth login para `gerencia@skilland.ai` | Pendiente — requiere navegador |
| Auth login para `direccion@skilland.ai` | Pendiente — requiere navegador |
| Aliases añadidos a `.zshrc` | Pendiente — acción manual opcional |
