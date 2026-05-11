# 2026-04-19 — PRs creados contra main en vez de dev post-v1.0.0

## Qué pasó
Creé PR #35 (HUD-301) y #36 (Diana mockups) con base `main` después del release-cut v1.0.0 (PR #34, dev → main). Flujo correcto del repo post-release es `feature → dev → main`.

## Causa raíz
Falta regla operativa sobre flujo de branches en `.claude/rules/workflow.md`. `gh pr create` sin `--base` cayó en el default del repo (`main`). La convención `dev → main` existía en la práctica (PR #31/32/33/34) pero nunca se capturó como regla persistente.

## Fix aplicado
`.claude/rules/workflow.md` — nueva sección "Git flow y PRs" con el flujo `feature → dev → main`, el comando canónico `gh pr create --base dev`, y la condición de setup (todos los repos del hub tienen `dev`).

## Complemento operativo pendiente (Juan)
Cambiar default branch de `agent-hub` y `hub-api` en GitHub de `main` → `dev` como defensa extra (cinturón + tirante).
