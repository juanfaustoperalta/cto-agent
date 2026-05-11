# 2026-04-19 — Context engineering: scope reducido, CLAUDE.md inmutable

## Qué pasó
HUD-301 arrancó ambicioso: condensar CLAUDE.md de los 5 agentes + mover secciones a `contexto/*.md` + rotación de "Reglas aprendidas". Objetivo: bajar baseline 40-50%.

Durante Fase 4 (piloto Carlos), cada sección candidata a mover resultó ser disparador de un comportamiento:
- "Aprendizaje y vault" → sin ella in-line, no sé dónde guardar decisiones cuando Juan lo pide.
- "hub-send tipos/flags" → sin ella, puedo usar un tipo mal.
- "inbox-first" → ya falla estando in-line (precedente HUD-304).

Juan impuso la regla: **"no borremos nada porque evidentemente todo es útil para que te comportes de acuerdo"**.

## Causa raíz
Framework del video YT (Imperio Digital — "Cómo estructurar agentes", ID `7HlfFHLoYK8`) recomienda `<200 líneas` en CLAUDE.md asumiendo que se puede separar "instrucciones inmutables" de "docs de referencia". En agentes del hub esa separación no existe limpiamente — cada sección es un gatillo de comportamiento. Bajar baseline sacrificando cualquier sección = drift garantizado.

## Fix aplicado
1. Scope de HUD-301 reducido a **solo habilitar auto-memory** en cwds de los 5 agentes (crear `memory/MEMORY.md` vacío en `~/.claude/projects/-Users-juanperalta-Documents-Projects-agent-hub-agents-{agente}/memory/`). Ejecutado — Carlos, Claudio, Laura, Diana, Lucas listos.
2. Fases 3-6 (audit, condensar, rotación) canceladas.
3. Memoria guardada: `feedback_claude_md_inmutable.md` — no se toca CLAUDE.md para optimizar.
4. Memoria complementaria: `feedback_behavior_over_context.md` — comportamiento > ahorro siempre.

## Replanteo para próximas iteraciones
Si el baseline sigue siendo problema, la palanca no es CLAUDE.md — es:
- Plugins user-scope (skills que no se usan) → auditar qué se puede desactivar
- Hooks estilo HUD-304 para reglas con drift comprobado (gate ejecutable, no texto)
- Auto-memory persistente (habilitado con este HUD) para no repetir aprendizaje entre sesiones
