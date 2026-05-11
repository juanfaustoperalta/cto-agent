# 2026-04-18 — fd exhaustion colapsó el hub

## Qué pasó

Claude Code y sesiones del hub empezaron a fallar con `directory no longer exists`, Bash con cwd inválido, Glob timeouts. Raíz: file descriptors del OS agotados (`Current limit: 2560` — default macOS) bajo carga normal: 5 Claude Codes + tmux + NATS + hub-api + hub-front + MCPs.

## Causa raíz (5 Whys)

`bin/start-hub` no ajustaba ulimit antes de lanzar servicios y no había doc del requisito. La escala (5 claudes + listener + menubar) es reciente (post-HUD-242 + HUD-256); antes con 1-2 sesiones no pegaba techo.

## Fix

`bin/start-hub` sube `ulimit -n` a 65536 al arrancar. Si falla (permisos), emite warn con comando para bumpear sistema: `sudo launchctl limit maxfiles 65536 524288`.

Complemento: regla en `agents/carlos/CLAUDE.md` para diagnosticar rápido el síntoma en el futuro.

Ref: HUD-271.
