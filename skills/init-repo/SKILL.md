---
name: init-repo
description: Initialize a project repository by creating standard `.gitignore`, `.cursorignore` files, an `AGENTS.md` context file at the project root for AI coding agents, and default scaffolding directories (`docs/`, `ref/`, `data/`, `output/`) with `.gitkeep` placeholders. Use when the user asks to initialize a repo, set up a new project structure, scaffold a Python project, or invokes `/init-repo`.
disable-model-invocation: true
---

# Init Repo

Create `.gitignore`, `.cursorignore`, and `AGENTS.md` in the current project root with the following default content.

## .gitignore

```
.env
__pycache__/
ref/
data/*
!data/.gitkeep
output/
.cursorignore
```

## .cursorignore

```
.env
__pycache__/
```

## AGENTS.md

```markdown
# AGENTS.md

> Guidance for AI coding agents (Cursor, Codex, Copilot, Claude Code, etc.) working in this repository.
> Keep this file concise (< 150 lines). Update it as the project evolves.

## Facts vs guessing (early project)

- Document only what is **known** from the repo, docs, or explicit user statements.
- If something is unknown (commands, stack details, deployment): write **`TBD`** and optionally what must be confirmed — **do not invent** commands, URLs, versions, or workflows.

## Project Overview

One or two sentences on purpose and primary tech stack. If unclear: **TBD**.

## Setup

How to install dependencies and run locally. If unclear: **TBD**.

- Python: use Conda env with Python 3.12 (adjust here if the project differs).
- Install deps via: `pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple` (skip if this repo does not use pip/requirements).

## Commands

List **exact** commands once verified (copy from README/Makefile/CI). Until then keep **`TBD`** — **do not guess** placeholders.

- Run (how developers execute the app or main pipeline): **TBD**
- Test (real test runner + scope): **TBD**
- Lint/format (only if the repo defines them): **TBD**

## Docs-only changes — how to verify

- Prefer **static checks**: re-read edited sections for accuracy, internal consistency, and commands/paths that actually exist in this repo.
- Fix or remove broken relative links; do not assume CI scripts exist.
- **Do not** run Test/Lint/Run entries above if they are still **TBD** or unrelated to the docs edit — that wastes time and invites fabricated commands.

## Project Structure

- `docs/`   — Project documentation
- `ref/`    — Local reference material (git-ignored)
- `data/`   — Datasets (git-ignored except `.gitkeep`)
- `output/` — Generated artifacts (git-ignored)

## Code Style & Conventions

- Communicate with the user in **Simplified Chinese**.
- Write Python code comments in **English**.
- Do not add narrative comments that merely restate code.
- Avoid introducing unrelated changes; keep edits minimally scoped.

## Tooling Conventions

- Use modern Docker CLI: `docker compose` (no hyphen).
- Use pip mirror: `https://pypi.tuna.tsinghua.edu.cn/simple`.

## Boundaries (Do NOT modify)

- **`.env`** — never read, print, modify, or commit; treat as secrets-bearing.
- **`ref/`** — local read-only reference; do not edit, reorganize, or rely on it for runnable code unless the user says otherwise.
- **External trees / MCP workspaces** — paths outside this repository root (e.g. other roots in a multi-root workspace, MCP-mounted dirs) are **not** this repo unless explicitly scoped by the user; do not apply repo-relative assumptions there or change files without clear intent.
- Do not create new files or directories unless explicitly requested.

## Git Workflow

Branch naming, PR expectations, and commit style. If unknown: **TBD**.

- Commit style: **TBD** (e.g. Conventional Commits once adopted).

## Notes

Gotchas, deployment, security. If none yet: **TBD**.
```

## Instructions

1. For `.gitignore` and `.cursorignore`: if the file already exists, ask the user whether to overwrite or append before making changes.
2. For `AGENTS.md`: do **not** ask the user. If it does not exist, create it from the template above. If it already exists, read its current content and silently append any of the template's standard sections (`Facts vs guessing (early project)`, `Project Overview`, `Setup`, `Commands`, `Docs-only changes — how to verify`, `Project Structure`, `Code Style & Conventions`, `Tooling Conventions`, `Boundaries (Do NOT modify)`, `Git Workflow`, `Notes`) that are missing — preserve all existing sections and content untouched.
3. Create `docs/`, `ref/`, `data/`, `output/` directories in the project root.
4. Create `data/.gitkeep` and `docs/.gitkeep` to ensure Git tracks these empty directories. `ref/` and `output/` do not need `.gitkeep` since they are fully ignored by Git.
5. If the user provides additional ignore patterns after the command (e.g. `/init-repo node_modules/ dist/`), append those patterns to both `.gitignore` and `.cursorignore`.
6. Always confirm the final content with the user after creation.
