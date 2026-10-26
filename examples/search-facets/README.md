# Walkthrough: Búsqueda con Facetas en ShopSearch

Ejemplo end-to-end del pipeline **PRD → Gemba Product Management Toolkit → `spec-input.md`** aplicado a una feature realista: añadir facetas inline en el buscador para que un cliente recurrente pueda desambiguar consultas comunes (ej: "café" → molido / grano / soluble / descafeinado).

**Por qué este ejemplo.** Ya tenemos el buscador en producción; ahora añadimos una mejora identificada por el equipo: el ~12% de queries son ambiguas, y la hipótesis es que un filtro de facetas inline reduce la fricción y mejora la conversión a "añadir al carrito".

> Este es un ejemplo **ilustrativo**: los artefactos son realistas pero los datos cuantitativos (farolas, métricas) son sintéticos. El feature concreto sí puede implementarse así.

---

## Flujo end-to-end

```
prd.md
  │
  ▼
/prd-quality-guard → /research → [entrevistas] → /analyze-research
  │
  ▼
/stories → /validate-stories → /split-stories → /prioritize
  │
  ▼
/handoff → spec-input.md → tu herramienta de SDD
```

---

## Estructura del walkthrough

| Fichero / carpeta | Contenido | Producido por |
|---------|-----------|---------------|
| `prd.md` | PRD de partida (problema, farolas, penumbras, métricas, alcance) | PM |
| `research/` | Guión de entrevistas (Mom Test) + notas de campo + JTBDs sintetizados | `/research` + entrevistas reales + `/analyze-research` |
| `stories/` | User stories validadas + reporte de priorización en batches | `/stories` + `/validate-stories` + `/split-stories` + `/prioritize` |
| `spec-input.md` | Entrada para la herramienta de SDD: stories con IDs estables, AC Given/When/Then, restricciones, métricas y preguntas abiertas | `/handoff` |

---

## Cómo seguir el ejemplo paso a paso

1. **Lee `prd.md`** — el contexto, la evidencia y el alcance de la feature
2. **Lee `research/`** — guión de entrevistas, notas y JTBDs sintetizados
3. **Lee `stories/`** — stories validadas con scoring 6D y batches priorizados
4. **Lee `spec-input.md`** — el entregable final: lo que pasarías a tu herramienta de SDD

Fíjate en que `spec-input.md` no añade nada que no esté en los pasos anteriores: traslada, normaliza los criterios a Given/When/Then y deja las dudas en "Preguntas abiertas".

---

## Importante

- **Los datos cuantitativos son sintéticos.** Los porcentajes (12% queries ambiguas, etc.) son ilustrativos, no datos reales de ShopSearch.
- **El feature es realista.** Las facetas en búsqueda son una funcionalidad estándar de e-commerce; este ejemplo enseña *el flujo*, no inventa una feature inverosímil.
- **El ejemplo termina en `spec-input.md`.** El plan técnico y la implementación los generaría la herramienta de SDD que uses; quedan fuera del plugin.
