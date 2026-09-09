# eco-mem

Portable four-duty memory for any agent. Protocol lives in one file; this page only points.

## How we work here

- Four duties, never one pile: working / semantic / episodic / procedural.
- INDEX first, entries on demand. Do not dump `.agents/memory/` into context.
- Working is a desk: promote or delete at task end. Do not archive the desk as history.
- Durable writes pass the gate in the skill. Guesses are not facts. Secrets are not memory.
- CRUD logic lives only in the skill. INDEX files are type contract + catalog.

## Where

- Protocol: `.agents/skills/eco-mem/SKILL.md`
- Memory: `.agents/memory/{working,semantic,episodic,procedural}/`

## Verify

Run from repo root. All four must pass.

```bash
test -f .agents/skills/eco-mem/SKILL.md
test -f .agents/memory/working/INDEX.md
test -f .agents/memory/semantic/INDEX.md
test -f .agents/memory/episodic/INDEX.md
test -f .agents/memory/procedural/INDEX.md
```

Catalog and files must match (no row without a file, no extra entry file):

```bash
python3 - <<'PY'
from pathlib import Path
root = Path('.agents/memory')
fail = 0
for kind in ('working', 'semantic', 'episodic', 'procedural'):
    d = root / kind
    idx = (d / 'INDEX.md').read_text()
    files = {p.name for p in d.glob('*.md') if p.name != 'INDEX.md'}
    linked = set()
    for line in idx.splitlines():
        if '.md' not in line or line.startswith('#') or 'INDEX.md' in line:
            continue
        for tok in line.replace('|', ' ').replace('`', ' ').replace(']', ' ').replace('(', ' ').replace(')', ' ').split():
            if tok.endswith('.md') and tok != 'INDEX.md' and '/' not in tok:
                linked.add(tok)
    extra = files - linked
    missing = linked - files
    if extra or missing:
        fail = 1
        print(f'{kind}: extra={sorted(extra)} missing={sorted(missing)}')
if fail:
    raise SystemExit(1)
print('catalog-ok')
PY
```

Never-store smoke (must print nothing):

```bash
rg -n -i 'api[_-]?key|secret|password|bearer |-----begin |cookie:' .agents/memory -g '!**/working/**' || true
```
