# youwrite

**An agent skill that refuses to write your code for you.**

AI coding assistants are optimized for speed, and most of the time that is the right
call. But some code you plan to live with — a method you will build on for years, a
pipeline your group has to maintain, a technique you want in your own hands. For that
code, a generated script is not the goal. Understanding is.

`youwrite` is a skill for those moments. Give it a task and it will not hand you the
finished script. It breaks the work into small chunks, explains the concept behind each
one, writes the scaffolding, and leaves the line that carries the idea to you:

```python
# TODO(you): convert IC50 in nM to pIC50
df["pIC50"] = ...
```

Then it stops and waits. Before anything runs, it asks what you think the output will
be — being wrong there is where things actually stick.

## What makes it different from "explain this code"

**It reviews code that already works.** A correct line can still get flagged: *"your
results file has no identifier column — these values cannot be traced back to a
compound."* Working and good are different things, and the gap between them is
judgment, which is the hardest part to absorb from generated code. It names at most two
issues and leaves the rewrite to you.

**It keeps an honest ledger.** Anything the agent generates without teaching goes into a
`## Not yours yet` list in `LEARNING.md`, and any line of it can be converted into a
lesson later. Unowned code is never invisible.

**It remembers across sessions.** `LEARNING.md` logs what you got wrong, not just what
was covered, and the next session opens with a recall question about it.

**It has an escape hatch.** Say "just do it" or "skip the teaching" and the skill drops
for that request, no argument. Teaching resumes at the next `/youwrite`.

## The one rule

> Never hand over a line the user could have written themselves.

Everything else in the skill serves that. The help level dials how much is *given*
(`demo` → `guide` → `nudge`), but never the mechanic: you write the marker yourself, you
predict output before running, one chunk per turn — at every level.

## Install

Claude Code, user-wide:

```bash
git clone https://github.com/GattiMh/youwrite-skill.git ~/.claude/skills/youwrite
```

Then invoke it with `/youwrite` followed by what you want to build:

```
/youwrite fetch IC50s for CDK2 from ChEMBL, convert to pIC50, plot the distribution
```

Project-scoped instead? Clone into `.claude/skills/youwrite` inside the repo.

## Other agents

The skill is a single markdown file with no vendor-specific code, no API calls, and no
tool definitions. All of its state lives in your project (`LEARNING.md`, `TODO(you)`
markers in your source), not in any agent's proprietary memory — so it ports.

| Agent | How |
|---|---|
| Claude Code | `~/.claude/skills/youwrite/SKILL.md` (auto-triggers from the description) |
| Codex CLI | copy the body to `~/.codex/prompts/youwrite.md` |
| Gemini CLI | TOML file in `~/.gemini/commands/`, body in the `prompt` field |
| Cursor | a rule file in `.cursor/rules/` |

Two caveats when porting. The stop-and-wait discipline runs against every coding agent's
bias toward finishing the task, and agentic IDEs are the most likely to "helpfully" fill
in your `TODO(you)` — you may need to make the hard-stops section more emphatic per
agent. And auto-triggering from the `description` field is Claude Code specific;
elsewhere you get explicit `/youwrite` invocation only, which is arguably better for a
mode this different from normal operation.

## The trade-off

This is slower than letting the agent write everything. Deliberately. It is not for
every task — it is for the code you want to still understand in six months.

## Colophon

The skill file was drafted with Claude and refined in use. Which is either ironic or
exactly the point, depending on how you look at it — the thing it protects against is
not *using* AI, it is ending up with code you never understood.

## License

MIT
