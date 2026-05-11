# cto-agent

Template del rol **CTO** (Chief Technology Officer) para el [`agent-hub`](https://github.com/juanfaustoperalta/agent-hub). Sparring estratégico de Juan en producto + arquitectura, roadmap técnico cross-project, ADRs proactivos, build-vs-buy, evaluación de modelos / tools / vendors, review de PRs.

Instalado, materializa al agente `carlos`. Sucede al template `em-agent` (Engineering Manager) — el cambio de rol agrega responsabilidades de visión técnica long-horizon y permite código off-critical-path (Charity Majors Pendulum: el CTO toca código para mantener calibración técnica sin volverse cuello de botella ni competir con devs).

## Instalación

```bash
cd ~/Documents/Projects/agent-hub
bin/install-agent carlos
```

`install-agent` clona este repo a `installed/agents/carlos/`, valida el `manifest.yaml`, y materializa la estructura híbrida en `agents/carlos/` (symlinks a los archivos del paquete + dirs runtime vacíos para `inbox/`, `tmp/`, `outputs/`).

## Contenido

- `CLAUDE.md` — identidad y reglas del rol CTO.
- `.claude/skills/` — skills role-specific (vienen del marketplace `agent-hub-skills` con `role: cto`).
- `aprendizajes/` — memoria del rol (append-only).
- `manifest.yaml` — contrato de paquete (ver [`docs/manifest.md`](https://github.com/juanfaustoperalta/agent-hub/blob/main/docs/manifest.md) en el hub).

## Diferencias vs em-agent

| Aspecto | em-agent (v1.6.x) | cto-agent (v1.0.0) |
| --- | --- | --- |
| Rol | Engineering Manager | Chief Technology Officer |
| Código | "NO escribo código" hard rule | Código off-critical-path OK (tooling, research, ADRs ejecutables, prototypes) |
| Horizonte | Sparring + review tactical | + roadmap técnico 6-12 meses, ADRs proactivos |
| Decisiones | Arquitectura existente | + build-vs-buy, eval modelos/tools/vendors |
| Sparring con Juan | Técnico | Técnico + producto |
| Critical-path | Marina implementa | Marina implementa (sin cambios) |

## Versionado

Ver [`CHANGELOG.md`](./CHANGELOG.md). Tag convention: `carlos-vX.Y.Z`. El cut de versión es siempre una decisión conjunta Carlos + Juan.

## Diseño

Spec del sistema de paquetes: [`projects/hub-project/specs/2026-04-20-agentes-paquetes-instalables-design.md`](https://github.com/juanfaustoperalta/agent-hub/blob/main/projects/hub-project/specs/2026-04-20-agentes-paquetes-instalables-design.md) en el hub.

## Referencias

- Charity Majors — *The Engineer/Manager Pendulum* (para CTOs, aplica con el pivote hacia código off-critical-path).
- Will Larson — *The Three CTO Models* (foundation / scaling / staff models).
- Edmond Lau — *The Effective Engineer* (early-stage CTO patterns).
