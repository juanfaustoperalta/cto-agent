---
promoted: 2026-04-19
---

# Post-compaction: verificar transcript antes de responder

**Fecha:** 2026-04-19
**Gatillo:** bug reportado por Juan — respondí sobre "Argentina" en vez de contestar "¿qué es la 277?".
**Postmortem:** `POSTMORTEM-2026-04-19.md` (raíz del repo office).

## Regla

Después de una compactación de contexto, **antes de responder al prompt que parece pendiente**, leer el último mensaje real del usuario desde el transcript JSONL de la sesión.

## Cómo detectar compactación reciente

Señales en el primer turno post-compactación:
- Aparece el marker `This session is being continued from a previous conversation that ran out of context. The summary below covers the earlier portion of the conversation.`
- Se inyectan system-reminders de skills con campo `ARGUMENTS:` — ese campo puede tener texto **viejo** de invocaciones previas, no el prompt actual.
- El `SessionStart hooks` tarda más de lo normal (típico <5s; anómalo ≥60s).

## Cómo verificar el último prompt real

Path del transcript:
```
~/.claude/projects/<cwd-hash>/<session-id>.jsonl
```

El `cwd-hash` es la ruta del cwd con slashes reemplazados por `-`. El `session-id` está en los records del propio JSONL.

Comando para extraer los últimos mensajes user de texto:

```python
import json
with open(path) as f:
    records = [json.loads(l) for l in f]
for r in records[-50:]:
    if r.get('type') != 'user':
        continue
    m = r.get('message', {})
    c = m.get('content', '')
    text = c if isinstance(c, str) else ''
    if isinstance(c, list):
        for item in c:
            if isinstance(item, dict) and item.get('type') == 'text':
                text = item.get('text', '')
    if text and not text.startswith('<'):
        print(r.get('timestamp'), text[:200])
```

El último registro de ese listado que **no** sea la propia invocación post-compactación (o el summary) es el prompt real a contestar.

## Por qué

El skill system re-inyecta `ARGUMENTS` de invocaciones pasadas como si fuera input actual. Sin verificación, uno responde a texto de ayer. Lección: el transcript del filesystem es la fuente de verdad; el context rebuilt post-compaction es aproximado.

## Qué no hacer

- Asumir que el primer texto prompt-shaped que veo post-compaction es fresco.
- Responder rápido porque "el contexto parece completo" — si hubo compactación, el contexto es una reconstrucción, no el original.

## Fix upstream

Reportar a `anthropics/claude-code`:
1. Re-inyección de skills post-compaction no debe traer `ARGUMENTS` viejos, o debe marcarlos con turno de origen.
2. El summarizer de compactación debe preservar verbatim el último user prompt sin respuesta.
3. Investigar `SessionStart hooks` >60s — superpowers/claude-code-warp como candidatos.
