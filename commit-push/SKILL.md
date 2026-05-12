---
name: commit-push
description: Commit and push code changes using Conventional Commits. Use when the user asks to commit, submit, push, or publish current git changes, especially when they want the agent to summarize code changes and push to the default branch.
---

# Commit Push

## Workflow

Use this skill when the user explicitly asks to commit and push changes.

1. Inspect the repository before staging:
   - Run `git status --short --branch --untracked-files=all`.
   - Run `git diff --staged` and `git diff`.
   - Run `git log --oneline -5` to learn the local commit style.
   - Run `git remote -v`.
   - Determine the default branch with `git symbolic-ref --short refs/remotes/origin/HEAD` or `git remote show origin`.

2. Default branch handling:
   - If the current branch is already the default branch, continue.
   - If the current branch is not the default branch and the default branch exists, preserve local work before switching.
   - If local changes would block switching, use a local stash with a clear message, switch to the default branch, then pop the stash.
   - If switching or stash pop causes conflicts, stop and ask the user.
   - If no default branch can be detected and no `dev`/`main` branch exists, stay on the current branch.

3. Sensitive file safety before `git add .`:
   - Check whether `.gitignore` exists.
   - Check whether `.gitignore` excludes common sensitive files, at minimum:
     - `.env`
     - `.env.*`
     - `*.key`
     - `*.pem`
     - `credentials.json`
     - `secrets.json`
     - `*.secret`
   - If sensitive rules are missing, add them to `.gitignore` before staging.
   - Do not read `.env` or print secret values.

4. Stage and verify:
   - Run `git add .`.
   - Run `git status --short --branch --untracked-files=all`.
   - If `.env`, credential files, keys, tokens, or other sensitive files are staged, unstage them and ensure `.gitignore` excludes them.
   - Review staged diff with `git diff --staged`.

5. Write a Conventional Commit message:
   - Format: `type(scope): summary`
   - Scope is optional. Use it only when the changed area is clear.
   - Keep the summary short, imperative, and accurate.
   - Prefer these types:
     - `feat`: new feature
     - `fix`: bug fix
     - `docs`: documentation only
     - `style`: formatting only, no behavior change
     - `refactor`: code restructuring without feature or bug fix
     - `perf`: performance improvement
     - `test`: tests only
     - `build`: build system or dependencies
     - `ci`: CI/CD changes
     - `chore`: maintenance, config, scripts, prompts, or tooling
     - `revert`: revert a previous commit

6. Commit:
   - Use a HEREDOC-style commit message.
   - Do not skip hooks.
   - If the commit fails because of hooks, fix the issue if straightforward, then create a new commit.
   - Do not amend unless the current conversation created the commit, the commit is unpushed, and a hook modified files after a successful commit.

7. Push:
   - Push to the detected default branch.
   - Use `git push -u origin HEAD` when upstream is missing.
   - Never force push unless the user explicitly requests it.
   - After pushing, run `git status --short --branch` and report the commit hash and branch.

## Commit Message Examples

```text
feat(auth): add token refresh flow
fix(api): handle empty response bodies
docs: update deployment guide
chore(config): switch default AI model
chore: refine procurement screening prompt
```
