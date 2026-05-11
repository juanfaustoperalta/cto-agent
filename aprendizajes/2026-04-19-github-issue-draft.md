# Draft — GitHub issue for anthropics/claude-code

**Status:** not filed yet. Awaiting Juan's approval before posting.

---

## Title

Post-compaction: stale skill `ARGUMENTS` re-surfaced as fresh prompt; last user message dropped from summary

## Body

**Claude Code version:** 2.1.114
**Plugins active:** `superpowers` 5.0.7, `claude-code-warp` 2.0.0 (both register `SessionStart` hooks)

### What happened

After an automatic context compaction, the assistant resumed by answering a `/investigate-first` invocation from ~17 hours earlier, ignoring the user's most recent prompt entirely.

### Timeline

| Time (UTC) | Event |
|------------|-------|
| T-17h | User invokes `/investigate-first "porque estabas buscando en argentina?"` — resolved same turn. |
| T-0  | User sends new, unrelated prompt: `"que es la 277?"` |
| T+100s | Automatic context compaction fires. |
| T+110s | `Running SessionStart hooks`… takes **1m 49s** (typical <5s). |
| T+230s | Assistant answers: *"No te sigo, no recuerdo haber buscado nada en Argentina..."* — replying to the 17h-old invocation, not the current prompt. |

### Root cause (two bugs compounded)

**1. Skill `ARGUMENTS` leak across compaction.**
The `<system-reminder>` re-injected for the `investigate-first` skill after compaction contained the `ARGUMENTS:` value from the prior day's invocation. The assistant had no way to distinguish "historical" args from "pending" args and treated the stale value as the current user input.

Evidence from the re-injected reminder:
```
### Skill: investigate-first
Path: projectSettings:investigate-first
...
ARGUMENTS: porque estabs buscando en argentina? no entiendo.
```

That literal string comes from a user record timestamped ~17 hours before the current turn.

**2. Compaction summary dropped the last unanswered user message.**
The summary (`This session is being continued...`) reconstructed dispatched tasks and recent assistant work but did not include the user's most recent text message (`"que es la 277?"`). Combined with bug #1, this left the stale `ARGUMENTS` as the only prompt-shaped signal in the rebuilt context.

### Suggested fixes

- Re-injected skill system-reminders should either (a) omit `ARGUMENTS` from prior turns, or (b) tag them with the turn/timestamp they originated in so the assistant can distinguish historical from pending.
- The compaction summarizer should preserve the most recent unanswered user prompt verbatim, not only summarize it.
- Investigate why `SessionStart hooks` took 1m 49s post-compaction — may or may not be related, but signals fragility in restoration.

### Workaround (user side)

After a compaction, read the session JSONL at `~/.claude/projects/<cwd-hash>/<session-id>.jsonl` and use the most recent `type: user` text record (excluding the compaction continuation record) as the real prompt to answer.
