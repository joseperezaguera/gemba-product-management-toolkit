# Spec input: ShopSearch Faceted Search

**Origen:** `prd.md` · **Fecha:** 2026-04-30 · **Generado por:** `/handoff`
**Estado:** Con preguntas abiertas

---

## 1. Contexto y problema

Los clientes recurrentes buscan por categoría conceptual ("café", "yogur") y reciben resultados de muchas categorías. Para afinar, escriben queries más largas o abandonan la búsqueda. El buscador ya tiene búsqueda exacta y tolerancia a typos; esta feature es la siguiente fase de su roadmap.

**Evidencia:**
- 12.3% de las queries son ambiguas (search service logs, abril 2026)
- 28% de abandono en queries ambiguas vs 16% en específicas (analytics, abril 2026)
- 41% de queries ambiguas van seguidas de otra query del mismo cliente en <30s (search analytics, abril 2026)
- "Acabo escribiendo 'café marca blanca molido' aunque tarde más, porque sale más limpio" (observación de campo, 2026-04-22)
- "Cuando no encuentro lo que quiero rápido, me voy y compro otra cosa" (entrevista, 2026-04-23)

## 2. Usuarios y JTBDs

### JTBD-01: Refinar búsqueda ambigua sin esfuerzo extra
- **Job:** Cuando busco un producto y la consulta es ambigua, quiero refinar el resultado sin pensar palabras adicionales, para llegar al producto correcto sin perder tiempo.
- **Struggle:** pensar palabras extra, scrollear resultados poco claros, abandonar.
- **Confianza:** Alta — 5/5 entrevistas mencionaron el struggle.

### JTBD-02: Reducir esfuerzo cognitivo en búsqueda mobile
- **Job:** Cuando uso la app en el móvil para comprar rápido, quiero que el buscador me ayude a afinar sin escribir más, para terminar antes de perder la paciencia.
- **Struggle:** pereza de escribir en mobile, abandonar si no es rápido.
- **Confianza:** Alta — 3/5 sesiones distinguieron mobile vs web; las otras 2 eran solo mobile.

## 3. Stories

### Batch 1 — Backend mínimo viable end-to-end

#### US-001 [P1] Clasificar query como ambigua o específica (alias: DETECT-01)

**Como** sistema de búsqueda
**cuando** recibo una query del cliente y devuelvo resultados
**quiero** clasificarla como `ambiguous` o `specific` según la dispersión de categorías en los top-K resultados
**para** decidir si la respuesta debe incluir facetas inline

- **JTBD:** JTBD-01
- **Cambio de comportamiento:** START — la respuesta pasa a incluir `query_classification`
- **Evidencia:** 12% de queries ambiguas (search service logs)
- **Depende de:** ninguna

**Criterios de aceptación:**
- AC-001.1 — Given un set de 50 queries de prueba (25 ambiguas, 25 específicas), When el sistema las clasifica, Then acierta en ≥90%.
- AC-001.2 — Given una query cualquiera, When el sistema la clasifica, Then la clasificación tarda <10ms p99.

#### US-002 [P1] Generar facetas con poder discriminatorio (alias: FACET-01/02)

**Como** sistema de búsqueda
**cuando** clasifico una query como `ambiguous`
**quiero** seleccionar 3-5 facetas de los atributos del catálogo con mayor poder discriminatorio
**para** ofrecer al cliente filtros que reduzcan la dispersión de los resultados

- **JTBD:** JTBD-01
- **Cambio de comportamiento:** START — la respuesta incluye un array `facets`
- **Evidencia:** 28% de abandono en queries ambiguas (analytics)
- **Depende de:** US-001

**Criterios de aceptación:**
- AC-002.1 — Given 25 queries de prueba ambiguas, When el sistema genera facetas, Then todas devuelven entre 3 y 5 facetas válidas.
- AC-002.2 — Given una faceta generada, When se inspeccionan sus valores, Then tiene entre 2 y 10.
- AC-002.3 — Given las facetas devueltas para el top-K, When se comparan con los atributos de esos productos, Then ≥80% de las facetas existen como atributo en ≥70% de los productos.

### Batch 2 — UI funcional con orden básico

#### US-003 [P2] Ordenar facetas y valores por relevancia (alias: FACET-03/04)

**Como** cliente recurrente
**cuando** veo facetas auto-generadas en los resultados de mi búsqueda
**quiero** que las facetas más usadas aparezcan primero y que los valores vayan de más a menos comunes
**para** elegir el filtro correcto sin leer todos los chips

- **JTBD:** JTBD-01
- **Cambio de comportamiento:** DIFFERENT — orden por relevancia en lugar de alfabético o aleatorio
- **Evidencia:** "Cuando busco 'café' me sale de todo y no sé cuál pillar" (entrevista, sesión 1)
- **Depende de:** US-002

**Criterios de aceptación:**
- AC-003.1 — Given una respuesta con facetas, When se comprueba su orden, Then están ordenadas de mayor a menor uso histórico.
- AC-003.2 — Given una faceta de la respuesta, When se comprueba el orden de sus valores, Then están ordenados de mayor a menor frecuencia en los resultados de la query.

#### US-004 [P2] Mostrar chips de facetas en mobile (alias: UI-01 a UI-05)

**Como** cliente recurrente que busca un producto en mobile
**cuando** mi query es ambigua y el sistema devuelve facetas
**quiero** ver chips horizontales debajo del search bar, con apply/remove rápido
**para** refinar la búsqueda en menos de 3 segundos sin escribir más

- **JTBD:** JTBD-02
- **Cambio de comportamiento:** START — nueva UI en la página de resultados
- **Evidencia:** 5/5 participantes eligieron chips inline frente a sidebar (discovery, abril 2026)
- **Depende de:** US-002

**Criterios de aceptación:**
- AC-004.1 — Given una respuesta con ≥3 facetas en mobile, When se muestran los resultados, Then los chips aparecen debajo del search bar.
- AC-004.2 — Given más chips de los que caben en pantalla, When el cliente desliza horizontalmente, Then puede ver el resto.
- AC-004.3 — Given chips visibles, When el cliente hace tap en uno, Then los resultados se filtran sin recargar la página.
- AC-004.4 — Given una faceta aplicada, When se muestra el chip, Then aparece en estado "active" con un botón ✕ que quita el filtro.
- AC-004.5 — Given un device de referencia mid-range, When se aplica o quita una faceta, Then la transición visual dura ≤300ms.

### Batch 3 — Telemetría y validación

#### US-005 [P3] Medir el uso de facetas (alias: TELEM-01 a TELEM-04)

**Como** equipo de producto
**cuando** el feature está en producción
**quiero** medir `facet_shown`, `facet_applied` y `facet_dismissed` con contexto de query
**para** decidir si el feature cumple los targets de §5

- **JTBD:** JTBD-01, JTBD-02 (medición)
- **Cambio de comportamiento:** START — nuevos eventos de analytics
- **Evidencia:** targets de §5
- **Depende de:** US-004

**Criterios de aceptación:**
- AC-005.1 — Given el feature activo, When se muestra, aplica o descarta una faceta, Then el evento correspondiente llega al pipeline de analytics estándar.
- AC-005.2 — Given eventos registrados, When se abre el dashboard, Then muestra `facet_apply_rate` y `cart_add_rate` segmentados por con/sin faceta.
- AC-005.3 — Given `facet_apply_rate` cae >30% de un día a otro, When se evalúa la alerta, Then se dispara.

## 4. Restricciones

- Latencia p99 del endpoint `/search` ≤195ms (baseline actual: 145ms).
- Con el feature flag desactivado, el endpoint responde igual que antes del feature.
- Rollout gradual con feature flag: 10% → 50% → 100% según métricas.

## 5. Métricas de éxito

| Métrica | Baseline | Target | Plazo | Cómo medir |
|---------|----------|--------|-------|------------|
| % queries ambiguas con faceta aplicada | N/A | ≥25% | 4 semanas tras GA | `facet_applied` / queries `ambiguous` |
| Abandono en queries ambiguas | 28% | 20% | 8 semanas tras GA | Analytics existente |
| Conversión a carrito en queries ambiguas | 18% | 19.5% | 8 semanas tras GA | Analytics existente |
| Latencia p99 de `/search` | 145ms | ≤195ms | Continuo | APM |

## 6. Fuera de alcance

- Multi-faceta combinada (≥2 facetas) — diferida a v2 para aprender del uso individual
- Facetas en queries no ambiguas — ruido visual sin valor diferencial
- Facetas curated a mano — no escala; se decidió generación algorítmica
- Facetas en búsqueda por voz — depende del roadmap de voz
- Sidebar de facetas — consume viewport en mobile-first
- Personalización por historial del cliente — v2

## 7. Preguntas abiertas

- [ ] ¿Rollout solo en mobile primero y web después? — JTBD-02 lo sugiere; decide PM.
- [ ] JTBD-03 ("confiar en el buscador como entrada principal") tiene confianza media — validar con la telemetría de US-005 antes de crear stories.
- [ ] AC-004.5: falta definir qué device es el "mid-range de referencia".

---

**Trazabilidad:** 5 stories · 15 AC · Score medio de calidad 8.4/10 (de `/validate-stories`)
