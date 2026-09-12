# Update from 2026-09-12
Eco-Mem already merged in [agents-stack](https://github.com/labs21-dev/agents-stack)

# eco-mem

Lightweight four-duty file memory for any agent. No database, no vector index, no runtime — markdown files an agent can already read and write.

Memory is not one drawer. For an agent to keep working, it must handle four duties separately: the present, knowledge, experience, and method. Mixing them into one pile is why an agent "gets it this time, then forgets next time." Remembering more is not doing better.

| Duty | Answers | Directory | Analogy |
|---|---|---|---|
| Working memory | What is happening now | `.agents/memory/working/` | Desk |
| Semantic memory | What is true | `.agents/memory/semantic/` | Revisable encyclopedia |
| Episodic memory | What happened before | `.agents/memory/episodic/` | Experience journal |
| Procedural memory | How this should be done | `.agents/memory/procedural/` | Operating procedure |

The four drawers are asymmetric: the desk disappears, the encyclopedia expires, the journal is append-only, and procedures prefer pointers to existing skills.

## Layout

```
.agents/skills/eco-mem/SKILL.md      sole protocol (philosophy, load, write-back gate, CRUD)
.agents/memory/working/INDEX.md      map of the current desk
.agents/memory/semantic/INDEX.md
.agents/memory/episodic/INDEX.md
.agents/memory/procedural/INDEX.md
AGENTS.md                            agent entry: pointers + verify
```

INDEX is a map, not a warehouse. Lookup is always **INDEX → pick rows → read those files**. Do not dump all of `memory/` into context.

CRUD lives only in the skill. INDEX files hold this drawer's duty contract + catalog.

## Port

Copy these two directories into any repo:

- `.agents/skills/eco-mem/`
- `.agents/memory/`

Add to the host `AGENTS.md`:

```markdown
## Memory
Protocol: `.agents/skills/eco-mem/SKILL.md`
Store: `.agents/memory/{working,semantic,episodic,procedural}/`
```

If the host skill directory is not `.agents/skills/`, copy only `SKILL.md` there. Do not create a second protocol. Working entries stay out of git (this repo's `.gitignore` already excludes them); the other three drawers may be committed.

## For agents

Full protocol: [`.agents/skills/eco-mem/SKILL.md`](.agents/skills/eco-mem/SKILL.md)

At task start, read the four INDEX files and open only entries relevant to this task. At task end: confirmed facts go to semantic, successes or failures with a lesson go to episodic, repeatedly proven methods go to procedural or graduate to a skill, then clear working.

When the AI "forgot," ask four questions before pouring in more memory: is the current task still on the desk? is the stable knowledge there? can similar past episodes be found? is there a verified way to do this?

## Verify

Commands live in [`AGENTS.md`](AGENTS.md). Run from repo root: the files exist, catalog rows match entry files, and the durable drawers contain no secrets.
