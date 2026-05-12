# Changelog

All notable changes to `cto-agent` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.2] — 2026-05-12 — Flow operacional: Carlos mergea directo (HUD-575)

### Changed

- **HUD-575** — `CLAUDE.md`: Carlos tiene merge authority para flow operacional. "NO mergeo a main sin aprobación de Juan" → "Mergeo por mi cuenta post review-pr-local". Sección nueva "Flow local Diana/Laura": Carlos prueba branch en local, abre PR + mergea. Juan no se invoca para merge cotidiano; solo cuando review revela decisión de negocio/scope nueva.

## [1.0.1] — 2026-05-12

### Changed

- **CLAUDE.md** — quitar regla heredada de EM "NO opero el hub". Reemplazo: "SÍ opero el hub" (instalación, configuración, fixes ops, deploys, debugging del runtime). Marina sigue como dispatcher operativo default por convención (mantiene flujo limpio + race conditions), pero CTO no depende de ella para acciones críticas. Distinción CTO vs EM: el EM era estratégico-tactical; el CTO también ejecuta cuando hace falta.
- **CLAUDE.md** — refinamiento de "Restricciones": NO escribir código de **producto critical-path** (eso es Claudio/Laura), pero SÍ escribir fixes operativos del hub (bin/, install scripts, configs) además del tooling off-critical-path original. Merge a main sigue requiriendo aprobación de Juan, pero CTO puede ejecutar el merge una vez autorizado (en vez de "NO mergeo" absoluto).

### Notes

- Bump **patch**: refinamiento de reglas, sin cambios estructurales del template.
- Tag base: `cto-v1.0.0`.
- Origen: corrección de Juan 2026-05-12 — el CTO de la oficina opera todo cuando hace falta, regla "NO opero el hub" venía del rol EM y no aplica al CTO.

## [1.0.0] — 2026-05-11

### Added

Initial release del template **CTO** (Chief Technology Officer). Sucede al template `em-agent` (Engineering Manager) con expansion de responsabilidades y permiso explícito para código off-critical-path.

**Nuevo respecto a em-agent v1.6.x:**

- **Identidad CTO**: sparring estratégico de Juan en producto + arquitectura, roadmap técnico cross-project (visión 6-12 meses), build-vs-buy, evaluación de modelos / tools / vendors, ADRs proactivos.
- **Código off-critical-path**: el CTO toca código en tooling interno, prototypes, scripts de research/eval, ADRs ejecutables, spikes técnicos. Sigue **NO** escribiendo critical-path (features de producto, fixes de prod, refactors cross-cutting) — eso es de devs. Esquema "Charity Majors Pendulum" para mantener calibración técnica sin volverse cuello de botella ni competir con devs.
- **6 actividades** (vs 4 en em-agent): sparring, review de PRs, decisiones arquitecturales, roadmap técnico cross-project, build-vs-buy + eval modelos, evolución del sistema.
- **Reglas aprendidas extendidas**: build-vs-buy default (integrar antes que construir, salvo lock-in / core diferenciador / costos a escala / requisitos críticos), eval de modelos por tarea (Opus para razonamiento + ADRs, Sonnet para coding + dispatching, Haiku para tooling background), ADRs proactivos cuando detecto patrón recurrente o trade-off futuro.

### Changed

- `manifest.yaml`: `role: em` → `role: cto`. `description` actualizada. `env.AGENT_ROLE: cto`. Bump major del versionado del template porque el contrato del rol cambia (las skills marketplace requieren agregar `cto` a `roles:` de las skills strategic relevantes para que `install-agent` las linkee al runtime dir de carlos).
- Skill `linear` reemplazada por `vikunja` en `requires.hub-skills` (alineación con stack actual post-ADR-009).

### Migration notes

- **Marketplace `agent-hub-skills`** requiere update: skills `role-specific/em/` deben extender `roles: [em, cto]` (review-pr-local, decision-canvas, postmortem-workflow) para que sigan llegando a `carlos` post-install. Sin ese update, post-install Carlos no ve las skills role-specific.
- **`bin/install-agent`** del hub debe soportar `role: cto` en su tabla de resolución (probablemente sin cambios — el script lee `role:` del manifest y usa ese path en marketplace, así que basta con que el marketplace exponga `role-specific/cto/` o que las skills `em/` se extiendan con rol `cto`).
- **El AGENT_VAULT_PATH se mantiene** (`carlos`) — el rebrand a CTO no cambia el path del perfil expandido del agente en el vault.

### Notes

- Bump **major** (1.0.0 fresh start): es un template nuevo independiente, no una evolución incremental del em-agent. El em-agent queda como referencia histórica del rol EM.
- Origen: research conjunta Carlos+Juan (2026-05-11) sobre el rol CTO vs EM en early-stage (refs: Charity Majors Pendulum, Will Larson three-CTO-models, Edmond Lau early-stage CTO). Snapshot del thread original referenciado en handoff `2026-05-11-2030-quick-tcc-restart-cto-agent-pending`.
- Tag base: nuevo repo, no hay tag previo.
