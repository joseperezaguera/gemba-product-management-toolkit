# Gemba User Story Toolkit

Pipeline de **definición de producto** para Claude Code: convierte un PRD (o una idea) en user stories validadas y priorizadas, empaquetadas como entrada para cualquier herramienta de **Spec-Driven Development** (SDD).

---

## ¿Qué hace?

Toma un documento de producto (PRD, o nada) y produce **user stories que aprobarías sin reescribir**: con JTBD, criterios de aceptación falsables, scoring de calidad y orden de entrega anti-waterfall.

```
PRD / vacío
    │
    ▼
[ Quality gate ] → [ Research design ] → [ Análisis ] →
    │
    ▼
[ Stories con JTBD ] → [ Validación ] → [ Splitting ] → [ Priorización ]
    │
    ▼
[ Handoff ] → spec-input.md
    │
    ▼
Tu herramienta de SDD (Spec Kit, Kiro, OpenSpec…) o tu equipo
```

Cubre el **qué** y el **por qué**: problema, JTBDs con evidencia, stories con criterios Given / When / Then y orden de entrega. El **cómo** (plan técnico, tareas, código) lo genera la herramienta de SDD que uses. El plugin no se acopla a ninguna: `spec-input.md` es Markdown neutro con IDs estables (`US-001`, `AC-001.1`) para mantener la trazabilidad.

---

## ¿Para quién?

- **PMs** que quieren stories con criterios falsables, no "user wants better UX"
- **Equipos de producto** que pasaron de Notion + Jira a un flujo asistido por agentes
- **Equipos pequeños** sin PM dedicado que quieren un proceso ligero pero riguroso
- **Equipos que usan Spec-Driven Development** y quieren que la spec parta de problemas validados, no de una idea sin contrastar

**No es para ti** si:
- Crees que las stories son una pérdida de tiempo y prefieres especificar todo en código
- Buscas un generador automático que rellene métricas inventadas (esta skill *se niega* a hacerlo)
- Tu producto es un experimento de fin de semana — overkill

---

## Instalación

### Como plugin de Claude Code (recomendado)

```bash
claude plugin marketplace add joseperezaguera/gemba-user-story-toolkit
claude plugin install gemba-user-story-toolkit@gemba
```

O desde una sesión de Claude Code: `/plugin marketplace add joseperezaguera/gemba-user-story-toolkit` y `/plugin install gemba-user-story-toolkit@gemba`.

---

## Uso rápido

### Tienes un PRD

```
/prd-quality-guard mi-prd.md     # Quality gate (≥7 → PASS)
/research                         # Diseñar entrevistas Mom Test
# ... realizas las entrevistas ...
/analyze-research                 # Generar JTBDs con evidencia
/stories                          # Convertir JTBDs → user stories
/validate-stories                 # Scoring 6D + antipatrones
/split-stories                    # Splits incrementales si son grandes
/prioritize                       # Batches anti-waterfall
/handoff                          # spec-input.md para tu herramienta de SDD
```

O todo seguido, con checkpoints entre pasos: `/pipeline`.

### No tienes nada

```
/build-story                      # Guía conversacional
/validate-stories
/split-stories
/prioritize
/handoff
```

---

## Ejemplo end-to-end

Ver [`examples/search-facets/`](examples/search-facets/): walkthrough completo del feature "filtro por facetas en búsqueda", desde el PRD hasta el `spec-input.md` final.

---

## Filosofía

**Modo copiloto, no piloto automático.**

Las skills NO inventan datos. Si falta información, te lo dicen y te preguntan. Si tu PRD no tiene métricas, no fabricarán números — el placeholder `[⚠️ Pendiente: definir con PM/Data]` aparece y te recomienda qué tipo de métrica sería útil.

Esto es deliberado. La razón: si la skill rellena por ti, dejas de pensar — y las stories sin pensamiento no soportan ejecución.

---

## Skills incluidas

| Skill | Rol |
|-------|-----|
| `prd-quality-guard` | Quality gate del PRD antes de research |
| `research-from-prd` | Gap detection + diseño de entrevistas Mom Test |
| `user-story-builder` | Crear stories desde cero (sin PRD) |
| `jtbd-to-stories` | Generar stories estructuradas desde JTBDs |
| `user-story-quality-coach` | Validar stories con scoring 6D + antipatrones |
| `story-splitting` | Detectar stories grandes y proponer splits |
| `story-prioritization` | Priorización con 5 lentes + grafo de dependencias |
| `sdd-handoff` | Empaquetar el resultado en `spec-input.md` para herramientas SDD |

---

## Contribuir

Ver [CONTRIBUTING.md](CONTRIBUTING.md). Reportes de bugs y PRs son bienvenidos.

---

## Licencia

MIT — ver [LICENSE](LICENSE).

---

## Créditos

Diseñado y mantenido por [José Ramón Pérez Agüera](https://www.linkedin.com/in/joseaguera/). Forjado en producción durante 2025-2026; generalizado y open-sourced en mayo 2026.

Inspiración: [Mom Test](http://momtestbook.com/) (Rob Fitzpatrick), [INVEST](https://en.wikipedia.org/wiki/INVEST_(mnemonic)), [Jobs-to-be-Done](https://hbr.org/2016/09/know-your-customers-jobs-to-be-done) (Christensen et al.).
