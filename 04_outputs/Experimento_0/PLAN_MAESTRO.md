# Plan Maestro — Experimento Google Workspace CLI

## Objetivo general

Evaluar con evidencia si Google Workspace CLI (`gws`) puede convertirse en una capa operativa seria para flujos de negocio agénticos.

## Principio guía

> Empezar con fundamentos. Aprender los primitivos. Explorar herramienta por herramienta. Construir hacia flujos realistas. Solo automatizar cuando lo básico esté entendido.

---

## Fase 1 — Setup y configuración

**Objetivo:** Ir de cero a primer comando exitoso con `gws`.

**Preguntas a responder:**
- ¿Cómo se instala `gws`?
- ¿Cuánta fricción hay con autenticación y permisos?
- ¿Es estable y reproducible el setup?
- ¿Qué scopes se necesitan y cómo se configuran?

**Criterios de avance:**
- [ ] `gws` instalado y funcional
- [ ] Autenticación completada contra una cuenta de Google Workspace
- [ ] Al menos un comando ejecutado con éxito (ej: listar ficheros de Drive)
- [ ] Documentada la fricción encontrada durante el setup

**Entregables:** `Fase_1_Setup/` — notas de instalación, config, primer comando, fricciones.

---

## Fase 2 — Exploración por herramienta

**Objetivo:** Mapear la superficie de `gws` producto por producto.

**Herramientas a explorar:**
- Gmail
- Drive
- Calendar
- Docs
- Sheets
- Admin (si aplica)

**Preguntas a responder:**
- ¿Qué puede hacer realmente cada herramienta?
- ¿Cómo es la estructura de comandos?
- ¿Qué outputs devuelve?
- ¿Es agradable de usar manualmente?
- ¿Es adecuado para agentes (outputs estructurados)?

**Criterios de avance:**
- [ ] Al menos 3 productos explorados con comandos documentados
- [ ] Para cada producto: lista de comandos disponibles, outputs de ejemplo, valoración inicial
- [ ] Identificados los productos más prometedores para flujos de negocio

**Entregables:** `Fase_2_Exploracion/` — un fichero por producto con comandos, outputs y valoración.

---

## Fase 3 — Casos de uso básicos

**Objetivo:** Testear flujos prácticos simples por herramienta.

**Ejemplos de casos:**
- Listar ficheros en Drive
- Buscar documentos por nombre/contenido
- Inspeccionar hilos de email
- Recuperar eventos de calendario
- Leer/escribir datos en Sheets

**Criterios de avance:**
- [ ] Al menos 5 casos de uso básicos ejecutados y documentados
- [ ] Cada caso incluye: comando, output, valoración de utilidad, fricciones
- [ ] Confianza construida en los primitivos antes de combinar

**Entregables:** `Fase_3_Casos_Basicos/` — casos documentados con resultados.

---

## Fase 4 — Flujos multi-paso compuestos

**Objetivo:** Testear flujos de negocio realistas que combinan múltiples productos.

**Ejemplos de flujos:**
- Buscar propuestas en Drive y resumirlas
- Inspeccionar contexto de email de un cliente antes de una reunión
- Combinar Drive + Gmail + Calendar para workflows de follow-up
- Extraer info de ventas desde docs y spreadsheets
- Flujos CRM-adjacent: seguimiento de leads y propuestas

**Criterios de avance:**
- [ ] Al menos 3 flujos multi-producto ejecutados
- [ ] Cada flujo documentado: pasos, comandos, outputs, valoración
- [ ] Identificados los flujos de mayor valor operativo

**Entregables:** `Fase_4_Flujos_Compuestos/` — flujos documentados con valoración.

---

## Fase 5 — Automatización y workflows programados

**Objetivo:** Explorar automatización solo después de entender los fundamentos.

**Ejemplos:**
- Cron jobs para reportes recurrentes
- Monitorización automatizada de inbox
- Rutinas operativas diarias/semanales
- Sistemas de soporte de negocio semi-autónomos

**Criterios de avance:**
- [ ] Al menos 1 automatización funcional implementada
- [ ] Documentados los límites y riesgos de automatizar con `gws`
- [ ] Evaluado el potencial real vs. el esfuerzo necesario

**Entregables:** `Fase_5_Automatizacion/` — automatizaciones implementadas y evaluadas.

---

## Fase 6 — Evaluación estratégica

**Objetivo:** Responder la pregunta central del experimento con evidencia.

**Preguntas a responder:**
- ¿En qué es bueno `gws`?
- ¿En qué no lo es?
- ¿Dónde es útil inmediatamente?
- ¿Dónde necesita madurar?
- ¿Para qué flujos de negocio aporta valor real?
- ¿Merece un lugar permanente en un stack agéntico de negocio?
- ¿Dónde supera al scripting custom?

**Criterios de avance:**
- [ ] Documento de evaluación completo con evidencia de las fases anteriores
- [ ] Recomendación clara de adopción (sí/no/parcial) con justificación
- [ ] Lista de flujos que vale la pena operacionalizar

**Entregables:** `Fase_6_Evaluacion_Estrategica/` — documento de conclusiones y recomendación.

---

## Dimensiones de evaluación transversales

Cada fase debería valorar (cuando aplique):

| Dimensión | Pregunta |
|---|---|
| Fricción de setup | ¿Qué tan doloroso o fluido es? |
| Usabilidad | ¿Es intuitivo para uso real? |
| Agent-friendliness | ¿Outputs estructurados y componibles? |
| Relevancia de negocio | ¿Resuelve algo realmente útil? |
| Fiabilidad | ¿Se puede confiar para uso repetido? |
| Potencial de automatización | ¿Prometedor para cron/workflows? |
| Tiempo ahorrado | ¿Elimina overhead manual real? |
| Apalancamiento estratégico | ¿Desbloquea flujos que antes eran inviables? |
