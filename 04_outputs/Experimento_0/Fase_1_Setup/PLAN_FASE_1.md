# Fase 1 — Setup y Configuración de Google Workspace CLI

## Objetivo

Ir de cero a primer comando exitoso con `gws`, documentando toda la fricción encontrada.

---

## Requisitos previos

Antes de empezar necesitamos verificar:

- [ ] **Node.js 18+** instalado (para instalar via npm)
- [ ] **Cuenta de Google** con acceso a Workspace (Gmail, Drive, etc.)
- [ ] **Acceso a Google Cloud Console** para crear proyecto y credenciales OAuth

Opcional pero útil:
- `gcloud` CLI instalado (simplifica el setup de auth)
- `jq` instalado (para procesar outputs JSON)

---

## Pasos detallados

### Paso 1 — Verificar requisitos del sistema

Comprobar versiones de Node.js y npm disponibles en el sistema.

```bash
node --version    # necesitamos >= 18
npm --version
```

Si falta Node.js, instalarlo primero.

### Paso 2 — Instalar `gws` globalmente

```bash
npm install -g @googleworkspace/cli
```

Verificar la instalación:
```bash
gws --version
gws --help
```

### Paso 3 — Configurar proyecto en Google Cloud

Dos caminos posibles:

**Camino A: Con `gcloud` instalado (más rápido)**
```bash
gws auth setup
```
Esto crea/configura el proyecto GCP automáticamente.

**Camino B: Manual (sin `gcloud`)**
1. Ir a https://console.cloud.google.com/
2. Crear un proyecto nuevo (o usar uno existente)
3. Ir a "APIs & Services" > "OAuth consent screen"
4. Tipo de app: **External**
5. Añadirte como **Test user**
6. Ir a "Credentials" > "Create Credentials" > "OAuth client ID"
7. Tipo: **Desktop app**
8. Descargar el JSON de credenciales
9. Guardarlo en `~/.config/gws/client_secret.json`

### Paso 4 — Autenticarse (login OAuth)

```bash
gws auth login
```

Esto abrirá el navegador para autorizar la app. Las credenciales se cifran en reposo con AES-256-GCM.

**Nota importante sobre scopes:**
Las apps en modo testing tienen límite de ~25 scopes. Para evitar problemas, podemos seleccionar solo los que necesitemos:

```bash
gws auth login -s drive,gmail,sheets,calendar
```

### Paso 5 — Verificar con primer comando

El "smoke test" — confirmar que todo funciona:

```bash
gws drive files list --params '{"pageSize": 5}'
```

Si devuelve una lista JSON de archivos de Drive, el setup está completo.

### Paso 6 — Exploración rápida de la CLI

Familiarizarnos con la estructura de comandos:

```bash
gws --help                      # servicios disponibles
gws drive --help                # recursos de Drive
gws gmail --help                # recursos de Gmail
gws schema drive.files.list     # schema de un método específico
```

---

## Cosas a documentar durante el proceso

Para cada paso, registrar:

| Aspecto | Qué anotar |
|---|---|
| Fricción | ¿Qué fue difícil, confuso o lento? |
| Errores | ¿Qué falló y cómo se resolvió? |
| Tiempo | ¿Cuánto tardó cada paso? |
| Sorpresas | ¿Algo inesperado (bueno o malo)? |
| Scopes | ¿Qué scopes se pidieron/configuraron? |
| Config | ¿Qué quedó en `~/.config/gws/`? |

---

## Criterios de avance (de PLAN_MAESTRO)

- [ ] `gws` instalado y funcional
- [ ] Autenticación completada contra una cuenta de Google Workspace
- [ ] Al menos un comando ejecutado con éxito
- [ ] Documentada la fricción encontrada durante el setup

---

## Entregable final de esta fase

Un fichero `RESULTADO_FASE_1.md` en esta misma carpeta con:
- Estado de cada paso (completado/fallido/parcial)
- Fricciones y errores encontrados
- Configuración final resultante
- Valoración de la experiencia de setup
- Captura de los outputs del primer comando exitoso
