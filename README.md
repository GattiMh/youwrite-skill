# youwrite

An agent skill that teaches while it builds. Instead of returning a finished script, it
breaks the task into small steps, explains each one, and leaves the key line for you to
write.

Works with Claude Code, Codex CLI, Antigravity, and other agents that read `SKILL.md`.

## How it works

Invoke it with a task:

```
/youwrite fetch IC50s for CDK2 from ChEMBL, convert to pIC50, plot the distribution
```

**1. It maps the task.** You get a numbered list of chunks before any code is written.
Each chunk is one new concept and something runnable at the end of it. You can reorder or
cut them before starting.

**2. Then one chunk at a time:**

- The concept is explained in plain English, before any code exists.
- The scaffolding is written — imports, function signature, the call site — with the
  instructive line left as a marker:

  ```python
  # TODO(you): convert IC50 in nM to pIC50
  df["pIC50"] = ...
  ```

- You write the marker. It will not fill it in unless you ask or you have attempted it
  three times.
- Before running, it asks what you expect the output to be.
- It runs the code and shows real output.
- It reviews what you wrote — including code that works, for naming, redundancy, and
  whether the output is actually usable. At most two comments, and it does not rewrite
  them for you.
- It stops and waits for you to continue.

## Help levels

Three levels control how much is given away. Switch at any time in plain words
("show me an example first", "just nudge me").

| Level | What you get |
|---|---|
| `demo` | The same move worked on different data first, then you repeat it. Concept gets an analogy. |
| `guide` | Concept in a few sentences, scaffolding, and what the marker must do. **Default.** |
| `nudge` | The marker and one line naming what it must do. You look the rest up. |

The level adjusts automatically if you stop needing hints, or if you need full hints
twice in a row. It never changes the mechanic: you write the marker, you predict output,
one chunk per turn, at every level.

## Skipping it

Say "just do it", "skip the teaching", or "go fast" and the skill drops for that
request and builds normally. It resumes at the next `/youwrite`.

## LEARNING.md

The skill keeps a `LEARNING.md` in your project root recording what each session covered,
what you got wrong, and any code that was generated without being taught (under
`## Not yours yet`). At the start of a session it reads the file and opens with a recall
question from previous material.

## Install

Clone into your agent's skills directory:

```bash
git clone https://github.com/GattiMh/youwrite-skill.git ~/.claude/skills/youwrite
```

| Agent | Path | Verified |
|---|---|---|
| Claude Code | `~/.claude/skills/youwrite/` | yes |
| OpenAI Codex CLI | `~/.codex/skills/youwrite/` | yes |
| Google Antigravity | `~/.gemini/config/skills/youwrite/` | yes |
| Cursor | `~/.cursor/skills/youwrite/` | reported |
| Gemini CLI | `~/.gemini/skills/youwrite/` | reported |
| Cline | `~/.cline/skills/youwrite/` | reported |

Rows marked *verified* were installed and confirmed — the agent lists `youwrite` among
its available skills. The rest come from published documentation and are untested here.
Project-scoped installs generally work too, most commonly under `.agents/skills/`.

For other agents:

```bash
npx skills add GattiMh/youwrite-skill
```

## Notes

The skill is a single markdown file using only `name` and `description` frontmatter, with
no vendor-specific fields. Its state lives in your project — `LEARNING.md` and `TODO(you)`
markers in your source — rather than in any agent's memory.

Two things vary by agent. Some are more likely than others to fill in a `TODO(you)`
rather than wait, since it runs against their default behavior. And automatic activation
from the `description` field is most reliable in Claude Code; elsewhere you may need to
invoke `/youwrite` explicitly.

The skill file was drafted with Claude.

## License

MIT
