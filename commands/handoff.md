---
name: handoff
description: "Empaquetar stories validadas y priorizadas en un spec-input.md neutro para cualquier herramienta de Spec-Driven Development"
---

> **Paso 0 (obligatorio): Cargar ground-rules.** Antes de proceder, lee `shared-config.md` y aplica:
> - **§Filosofía del Plugin** — modo copiloto, NO inventar, preguntar antes de generar
> - **§Estilo de Escritura** — frases ≤30 palabras, sin adjetivos sin datos, NUNCA inventar métricas
> - **§Antipatrones Compartidos** — los 7 antipatrones a detectar
>
> Si no puedes leer el fichero, detén la ejecución y reporta el problema.

Ejecuta `/sdd-handoff` para generar el documento de entrada a tu herramienta de SDD.

**Input necesario:** Stories validadas (obligatorio). Opcionales: batches de `/prioritize`, JTBDs de `/analyze-research`, PRD original.

**Proceso:**
1. Leer el SKILL.md de `skills/sdd-handoff/`
2. Inventariar artefactos disponibles → Checkpoint con usuario
3. Asignar IDs estables (`US-NNN`, `AC-NNN.N`)
4. Normalizar criterios de aceptación a Given / When / Then
5. Rellenar `references/spec-input-template.md` sin añadir nada que no esté en los artefactos
6. Revisión de neutralidad (sin arquitectura, sin sintaxis de herramientas concretas)
7. Checkpoint final → guardar `spec-input.md`

**Output:** `spec-input.md`.

**Siguiente paso:** Usar `spec-input.md` como entrada de la fase de especificación de tu herramienta de SDD (o entregarlo al equipo). Para ir por batches, pasa solo el batch actual más las secciones comunes.
