---
name: sdd-handoff
description: |
  Empaqueta el resultado del pipeline (PRD + JTBDs + stories validadas y priorizadas) en un
  único `spec-input.md` en Markdown neutro, listo para ser la entrada de cualquier herramienta
  de Spec-Driven Development (Spec Kit, Kiro, OpenSpec, BMAD...) o de un equipo humano.

  POSICIÓN EN EL PIPELINE:
  - DESPUÉS de /prioritize (recibe batches priorizados)
  - ÚLTIMO paso del plugin: aquí termina el "qué y por qué"; el "cómo" lo decide la herramienta SDD
  - Puede ejecutarse sobre cualquier conjunto de stories validadas (sin priorizar → un solo batch)

  USAR CUANDO:
  - El pipeline ha terminado y el usuario quiere pasar el trabajo a su herramienta de SDD
  - El usuario pide "exportar", "empaquetar", "spec de entrada", "handoff"
  - Comando: /handoff

  NO USAR CUANDO:
  - Las stories no han pasado /validate-stories (validar primero)
  - El usuario pide el plan técnico, la arquitectura o las tareas de implementación (eso lo genera la herramienta SDD)
  - El usuario pide el formato nativo de una herramienta concreta (este plugin no se acopla a ninguna)
---

# SDD Handoff

Convierte los artefactos del pipeline en un único documento de entrada para Spec-Driven Development.

> **Modo copiloto:** Esta skill TRASLADA, no genera. Cada línea del `spec-input.md` debe salir de un artefacto previo (PRD, JTBDs, stories, priorización). Lo que no esté, se marca como pendiente. Ver shared-config.md §Filosofía del Plugin.

## Principio Fundamental

**El plugin entrega el qué y el por qué. La herramienta SDD decide el cómo.**

El `spec-input.md` describe problema, usuarios, comportamiento esperado y criterios verificables. No contiene arquitectura, stack, modelos de datos ni tareas técnicas: eso lo produce la fase de plan de la herramienta SDD.

---

## Integración con el Pipeline

```
... → validate-stories → split-stories → prioritize → 🆕 HANDOFF
                                                          │
                                                          ▼
                                                   spec-input.md
                                                          │
                                                          ▼
                                     Spec Kit / Kiro / OpenSpec / equipo
```

### Input esperado

| Artefacto | Obligatorio | De dónde sale |
|---|---|---|
| Stories validadas (Como/Cuando/Quiero/Para + AC + scoring 6D) | Sí | `/validate-stories` (y `/split-stories` si aplica) |
| Batches priorizados | No | `/prioritize`. Si falta → un único batch, marcado como "sin priorizar" |
| JTBDs con evidencia | No | `/analyze-research`. Si falta (Ruta sin documento) → sección marcada como pendiente |
| PRD | No | El PRD original. Aporta problema, métricas y exclusiones |

### Output

Un único fichero `spec-input.md` con la estructura de `references/spec-input-template.md`.

---

## Proceso

### Paso 1: Inventario de artefactos

Listar qué artefactos hay y cuáles faltan. Mostrarlo al usuario.

**CHECKPOINT:** "Tengo [N] stories validadas, [batches sí/no], [JTBDs sí/no], [PRD sí/no]. ¿Falta algo que quieras aportar antes de empaquetar?"

### Paso 2: Asignar IDs estables

- Stories: `US-001`, `US-002`... en el orden de los batches.
- Criterios de aceptación: `AC-001.1`, `AC-001.2`... por story.
- JTBDs: conservar el ID que traigan (`JTBD-01`). Si no tienen, `JTBD-01`, `JTBD-02`...
- Si las stories ya tienen IDs del equipo (p. ej. `DETECT-01`), conservarlos como alias: `US-001 (DETECT-01)`.

Los IDs son la trazabilidad entre este documento y lo que genere la herramienta SDD. No renumerar en iteraciones posteriores: las stories nuevas toman el siguiente número libre.

### Paso 3: Normalizar criterios de aceptación

Cada AC en formato **Given / When / Then**. Si un AC viene como checklist ("Tap aplica faceta sin recargar"), reescribirlo conservando el significado exacto:

```
- AC-004.3 — Given una query ambigua con chips visibles, When el cliente hace tap en un chip, Then los resultados se filtran sin recargar la página.
```

Si un AC no se puede reescribir sin inventar el contexto (Given) → dejarlo literal y marcarlo `⚠️ Pendiente: precisar contexto`.

### Paso 4: Trasladar secciones

Rellenar la plantilla sección por sección. Para cada una:

| Sección | Fuente | Si falta |
|---|---|---|
| Contexto y problema | PRD §Problema + Farolas/Penumbras | `⚠️ Pendiente: sin PRD` |
| Usuarios y JTBDs | JTBDs de `/analyze-research` o JTBD Reforzado de las stories | Usar el JTBD Reforzado de cada story |
| Stories por batch | Stories + batches | Un batch "sin priorizar" |
| Métricas de éxito | PRD §Métricas (baseline → target) | `⚠️ Pendiente: definir con PM/Data` |
| Fuera de alcance | PRD §Exclusiones + stories descartadas en splitting | `⚠️ Pendiente` |
| Preguntas abiertas | Gaps marcados en cualquier artefacto + preguntas pendientes de los checkpoints | "Ninguna" solo si de verdad no hay |

### Paso 5: Revisión de neutralidad

Antes de entregar, escanear el documento y eliminar:
- Decisiones técnicas que no vengan del PRD como restricción explícita (stack, librerías, esquemas).
- Referencias a herramientas de SDD concretas o a su sintaxis (`/speckit.*`, `.kiro/`, `openspec/`).
- Tareas de implementación ("crear tabla", "añadir endpoint").

Las **restricciones** del PRD (latencia, compatibilidad, regulación) sí se trasladan, en la sección de restricciones.

**CHECKPOINT:** Mostrar el `spec-input.md` completo y preguntar: "¿Lo guardo como `spec-input.md`?"

---

## Cómo se usa la salida

El `spec-input.md` es el prompt de entrada para la fase de especificación de cualquier herramienta SDD. Ejemplos orientativos (no exhaustivos, el plugin no depende de ninguno):

- Pegarlo como descripción de la feature en el comando de especificación de la herramienta.
- Usarlo como documento de requisitos de partida y dejar que la herramienta genere diseño y tareas.
- Entregarlo a un equipo humano como contrato de la iteración.

Para entregar por batches, pasar a la herramienta SDD solo el batch actual + las secciones comunes (contexto, JTBDs, métricas, fuera de alcance).

---

## Reglas Estrictas

1. **NUNCA** añadir contenido que no esté en un artefacto previo. Lo que falte se marca `⚠️ Pendiente`.
2. **NUNCA** incluir arquitectura, stack, modelos de datos ni tareas técnicas: eso es trabajo de la herramienta SDD.
3. **NUNCA** acoplar la salida a la sintaxis de una herramienta concreta. Markdown neutro.
4. **SIEMPRE** asignar IDs estables (`US-NNN`, `AC-NNN.N`) y conservar los IDs previos como alias.
5. **SIEMPRE** expresar los AC en Given / When / Then, sin cambiar su significado.
6. **SIEMPRE** enlazar cada story a su JTBD y a su evidencia (fuente de la Farola o Penumbra).
7. **SIEMPRE** listar las preguntas abiertas: la herramienta SDD necesita saber qué no está decidido.
8. **NUNCA** guardar el fichero sin el CHECKPOINT final con el usuario.
