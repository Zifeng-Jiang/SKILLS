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

## Project Overview

<!-- TODO: One or two sentences describing what this project does and its primary tech stack. -->

## Setup

<!-- TODO: How to install dependencies and run the project locally. -->
- Python: use Conda env with Python 3.12
- Install deps via: `pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple`

## Commands

<!-- TODO: Replace with the real build / run / test / lint commands. -->
- Run: `<command>`
- Test: `<command>`
- Lint: `<command>`

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

- `.env` files — never read or modify.
- Any file under `ref/` — read-only reference material.
- Do not create new files or directories unless explicitly requested.

## Git Workflow

<!-- TODO: Describe branch naming, commit message style, PR process. -->
- Commit style: <!-- e.g. Conventional Commits -->

## Notes

<!-- Anything else an AI agent should know: gotchas, deployment steps, security, etc. -->
```

## Instructions

1. For `.gitignore` and `.cursorignore`: if the file already exists, ask the user whether to overwrite or append before making changes.
2. For `AGENTS.md`: do **not** ask the user. If it does not exist, create it from the template above. If it already exists, read its current content and silently append any of the template's standard sections (`Project Overview`, `Setup`, `Commands`, `Project Structure`, `Code Style & Conventions`, `Tooling Conventions`, `Boundaries (Do NOT modify)`, `Git Workflow`, `Notes`) that are missing — preserve all existing sections and content untouched.
3. Create `docs/`, `ref/`, `data/`, `output/` directories in the project root.
4. Create `data/.gitkeep` and `docs/.gitkeep` to ensure Git tracks these empty directories. `ref/` and `output/` do not need `.gitkeep` since they are fully ignored by Git.
5. If the user provides additional ignore patterns after the command (e.g. `/init-repo node_modules/ dist/`), append those patterns to both `.gitignore` and `.cursorignore`.
6. Always confirm the final content with the user after creation.
