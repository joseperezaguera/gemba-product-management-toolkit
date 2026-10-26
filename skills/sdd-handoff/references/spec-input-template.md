# Plantilla: spec-input.md

> Reference data for SKILL.md. Loaded on demand.

Estructura del documento de entrada para cualquier herramienta de Spec-Driven Development. Markdown neutro: sin sintaxis propia de ninguna herramienta.

```markdown
# Spec input: [Nombre de la feature]

**Origen:** [PRD / sesión de /build-story] · **Fecha:** [YYYY-MM-DD] · **Generado por:** `/handoff`
**Estado:** [Listo para especificar | Con preguntas abiertas]

---

## 1. Contexto y problema

[2-5 frases del PRD §Problema: quién sufre el problema, desde cuándo, por qué ahora.]

**Evidencia:**
- [Farola con valor y fuente]
- [Penumbra literal con fuente]

## 2. Usuarios y JTBDs

### JTBD-01: [Nombre]
- **Job:** Cuando [situación], quiero [motivación], para [resultado].
- **Struggle:** [fricción principal]
- **Confianza:** [Alta/Media/Baja] — [base de la confianza]

## 3. Stories

### Batch 1 — [Valor que entrega el batch]

#### US-001 [P1] [Título corto] (alias: [ID previo si existe])

**Como** [usuario específico]
**cuando** [situación]
**quiero** [capacidad]
**para** [resultado]

- **JTBD:** JTBD-01
- **Cambio de comportamiento:** [START/STOP/DIFFERENT] — [ahora → nuevo]
- **Evidencia:** [Farola/Penumbra + fuente]
- **Depende de:** [US-00X | ninguna]

**Criterios de aceptación:**
- AC-001.1 — Given [contexto], When [acción], Then [resultado observable].
- AC-001.2 — Given …, When …, Then ….

### Batch 2 — […]

[…]

## 4. Restricciones

[Solo las que el PRD fija como restricción explícita: latencia, compatibilidad, regulación, plazos externos. Sin decisiones de diseño.]

## 5. Métricas de éxito

| Métrica | Baseline | Target | Plazo | Cómo medir |
|---------|----------|--------|-------|------------|

## 6. Fuera de alcance

- [Exclusión] — [razón]

## 7. Preguntas abiertas

- [ ] [Pregunta] — [quién decide / qué artefacto la originó]

---

**Trazabilidad:** [N] stories · [N] AC · Score medio de calidad [X.X]/10 (de `/validate-stories`)
```

## Prioridad

`[P1]`, `[P2]`, `[P3]` = número del batch en el que está la story. No es un nuevo scoring: refleja el orden decidido en `/prioritize`. Sin priorización → todas `[P1]` y estado "sin priorizar".

## Lo que NO va en el documento

| No incluir | Por qué |
|---|---|
| Arquitectura, stack, librerías | Lo decide la fase de plan de la herramienta SDD |
| Esquemas de datos, endpoints | Ídem |
| Tareas de implementación | Ídem |
| Scoring 6D completo | Es diagnóstico interno; basta el score medio en la trazabilidad |
| Guiones de entrevista, notas de campo | Se quedan en los artefactos de research; aquí solo la evidencia citada |
