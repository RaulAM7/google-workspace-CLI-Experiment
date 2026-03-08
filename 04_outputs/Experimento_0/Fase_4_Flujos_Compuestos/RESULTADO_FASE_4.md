# Resultado Fase 4 — Flujos multi-paso compuestos

**Fecha:** 2026-03-08
**Estado:** Completada

---

## Flujos ejecutados

### Flujo 1 — Weekly Deal Report (ejecutado en caso práctico Fase 3)

**Productos combinados:** Gmail + Calendar + Análisis + Envío HTML

**Pasos:**
1. Gmail: extraer 12 hilos de label "04.- Skilland MicroCred" con metadata y snippets
2. Calendar: consultar agenda de marzo completo, filtrar eventos Skilland
3. Análisis: clasificar deals por estado (caliente/follow-up/sin respuesta)
4. Generación: crear reporte markdown con métricas y next steps
5. Envío: convertir a HTML y enviar via `gws gmail users messages send`

**Resultado:** Reporte enviado a raulartilesm@gmail.com y fer@reboot.academy con formato HTML profesional.

**Valoración:** Muy alta. Este flujo tiene valor operativo inmediato. Un agente podría ejecutarlo cada lunes.

---

### Flujo 2 — Meeting Prep: Universidad de Jaén (18 marzo)

**Productos combinados:** Calendar + Gmail + Drive + Docs

**Pasos:**
1. Calendar: obtener detalle del evento (asistentes, Meet link, confirmaciones)
2. Gmail: recuperar hilo completo de conversación con Paco Roca (froca@ujaen.es)
3. Drive: buscar documentos de microcredenciales relacionados
4. Docs: extraer texto de la Propuesta Piloto Edukami/Moodle como referencia

**Briefing generado:**

**Reunión:** Proyecto Microcredenciales | Universidad Jaén <> EduKami
- **Fecha:** 18 marzo 2026, 10:00-11:00 (hora canaria)
- **Meet:** https://meet.google.com/mip-ykia-kbq
- **Asistentes:**
  - Raúl (raul@reboot.academy) — aceptado
  - Romi (romi@reboot.academy) — pendiente
  - Paco Roca (froca@ujaen.es) — pendiente
  - magc@ujaen.es — aceptado

**Contexto de la relación:**
- Contacto inicial post-Transfiere (2 mar). Raúl presenta modelo de microcredenciales.
- Paco responde el mismo día: "Me parece muy interesante lo que planteáis, será un placer retomar la conversación."
- Reunión agendada para 18 mar. Paco confirma: "Perfecto, el día 18 de 11:00 a 12:00 nos viene bien."
- Tono: muy positivo, interés genuino, celeridad en las respuestas.

**Documentos relevantes en Drive:**
- Solicitud de Microcredenciales - Plan MicroCreds (.docx, 5 mar)
- Propuesta Microcredenciales IA (Google Doc, 19 feb)
- Microcredenciales - Listado de contactos (.xlsx, 19 feb)
- Propuesta Piloto Edukami/Moodle v.2 (Google Doc, nov 2025)

**Fermín Lucena (UJA)** aparece en el funnel Transfiere como contacto de interés. No hay emails previos con él — posible segundo interlocutor a explorar.

**Valoración:** Alta. Combinar 4 fuentes en un briefing pre-reunión es exactamente lo que necesita un agente de business development.

---

### Flujo 3 — Pipeline Review: Funnel Transfiere + Gmail

**Productos combinados:** Sheets + Gmail

**Pasos:**
1. Sheets: leer 20 leads del funnel Transfiere (nombre, organización, interés)
2. Gmail: cruzar cada lead con búsqueda de emails para detectar si ya hay contacto

**Resultado del cruce:**

| Lead | Organización | Interés | Contacto por email |
|---|---|---|---|
| Javier Montiel | U. Alicante | Skilland MicroCred | Sí (hilo activo) |
| Ana Ramírez | INTA | Skilland MicroCred | Sí (redirigido a formacion@inta.es) |
| Cristina Cabeza | Andalucía TRADE | MicroCred + Pro | Sí (emails encontrados) |
| Fermín Lucena | U. Jaén | Skilland MicroCred | No — sin contacto email |
| Fernando Conesa | UPV | Skilland MicroCred | No — sin contacto email |

20 leads en funnel, al menos 3 con conversación activa, 2+ sin contactar aún.

**Valoración:** Muy alta. Cruzar un spreadsheet de leads con el historial de email es la base de un CRM-adjacent funcional. Un agente podría generar este cruce semanalmente y alertar sobre leads sin contactar.

---

### Flujo 4 — Búsqueda de propuestas + resumen

**Productos combinados:** Drive + Docs

**Pasos:**
1. Drive: buscar documentos con "microcredencial" o "propuesta" en el nombre
2. Docs: extraer texto completo de la Propuesta Piloto Edukami/Moodle

**Resultado:** Texto completo extraído. Propuesta de integración técnica Edukami/Moodle para FGULL:
- Plugin Moodle + SSO via LTI 1.3 para autoría
- Exportación HTML nativa + fallback SCORM 1.2
- 20 licencias Edukami, 6 meses
- Cumplimiento RGPD, ISO 27001
- Objetivo: 10-15 microcredenciales para nov 2025

**Valoración:** Alta. Un agente puede buscar, leer y resumir propuestas automáticamente. Útil para preparar reuniones, revisar historial de deals, o generar comparativas.

---

## Resumen de valoración

| Flujo | Productos | Valor operativo | Automatizable |
|---|---|---|---|
| Weekly Deal Report | Gmail + Calendar + envío HTML | Muy alto | Sí (semanal) |
| Meeting Prep | Calendar + Gmail + Drive + Docs | Alto | Sí (pre-reunión) |
| Pipeline Review | Sheets + Gmail | Muy alto | Sí (semanal) |
| Propuestas + resumen | Drive + Docs | Alto | Sí (bajo demanda) |

---

## Fricciones

1. **Gmail search devuelve 201 para cualquier resultado >0**: el `resultSizeEstimate` no es preciso, solo indica "hay resultados". No se puede saber el número real sin paginar.
2. **Escapado JSON en shell sigue siendo un dolor** para queries complejas con comillas anidadas.
3. **Envío HTML requiere construir MIME manualmente** — el helper `+send` solo soporta texto plano.

---

## Criterios de avance — verificación

- [x] Al menos 3 flujos multi-producto ejecutados (4 de 4)
- [x] Cada flujo documentado: pasos, comandos, outputs, valoración
- [x] Identificados los flujos de mayor valor: Weekly Deal Report y Pipeline Review

**Fase 4 cerrada. Lista para avanzar a Fase 5.**
