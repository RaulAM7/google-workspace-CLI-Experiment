# Fase 2 — Exploración por herramienta

## Objetivo

Mapear la superficie real de `gws` producto por producto: qué comandos hay, qué devuelven, qué tan agent-friendly son, y cuáles son los más prometedores para flujos de negocio.

## Orden de exploración (por relevancia de negocio)

1. **Gmail** — email es el centro de la comunicación comercial
2. **Drive** — documentos, propuestas, ficheros compartidos
3. **Calendar** — reuniones, agenda, coordinación
4. **Sheets** — tracking operativo, datos estructurados
5. **Docs** — propuestas, documentos comerciales
6. **Extras** — Tasks, Workflow, y otros si el tiempo lo permite

## Método por producto

Para cada producto:
1. `gws <servicio> --help` — listar recursos y helpers
2. Probar cada helper (`+`) con datos reales
3. Probar 2-3 comandos API directos relevantes
4. Evaluar: outputs, usabilidad, agent-friendliness
5. Documentar en fichero individual

## Criterios de avance (de PLAN_MAESTRO)

- [ ] Al menos 3 productos explorados con comandos documentados
- [ ] Para cada producto: lista de comandos, outputs de ejemplo, valoración inicial
- [ ] Identificados los productos más prometedores para flujos de negocio

## Entregable

Un fichero por producto: `gmail.md`, `drive.md`, `calendar.md`, `sheets.md`, `docs.md`
