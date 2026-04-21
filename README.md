# rdl-cross-ecosystem-root

Test fixture for the `remove_duplicate_lockfiles` maintenance task.

## Scenario

Two independent conflicts in the **same directory** but in different ecosystems. Exercises the MT's per-(directory, ecosystem) grouping.

Files at root:
- `package.json` — no `packageManager`, no node config signals → **node conflict is ambiguous**.
- `package-lock.json`, `yarn.lock`.
- `pyproject.toml` with `[tool.uv]` → **python conflict auto-resolves to uv**.
- `poetry.lock`, `uv.lock`.

## Expected MT behaviour

- Emits two `LockfileConflict` entries for the root directory — one per ecosystem.
- Python conflict is auto-resolved (uv). No question for it.
- Node conflict raises a `select` question: `["npm", "yarn"]`, default `npm` (first option, since pnpm default isn't present).
- Plus the `update_gitignore` boolean.
- After apply: `poetry.lock` deleted and whichever node lockfile the user didn't pick is deleted. `.gitignore` lists both.
