---
name: init-repo
description: Initialize a project repository by creating standard `.gitignore`, `.cursorignore` files and default scaffolding directories (`docs/`, `ref/`, `data/`, `output/`) with `.gitkeep` placeholders. Use when the user asks to initialize a repo, set up a new project structure, scaffold a Python project, or invokes `/init-repo`.
disable-model-invocation: true
---

# Init Repo

Create `.gitignore` and `.cursorignore` in the current project root with the following default content.

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

## Instructions

1. If `.gitignore` or `.cursorignore` already exists, ask the user whether to overwrite or append before making changes.
2. Create `docs/`, `ref/`, `data/`, `output/` directories in the project root.
3. Create `data/.gitkeep` and `docs/.gitkeep` to ensure Git tracks these empty directories. `ref/` and `output/` do not need `.gitkeep` since they are fully ignored by Git.
4. If the user provides additional ignore patterns after the command (e.g. `/init-repo node_modules/ dist/`), append those patterns to both files.
5. Always confirm the final content with the user after creation.
