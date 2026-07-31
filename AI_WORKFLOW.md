# Shared AI collaboration protocol

This repository is edited alternately by the user, Codex, and Claude. This file is the shared source of truth for coordination. `AGENTS.md` and `CLAUDE.md` require both agents to follow it.

## Non-negotiable rules

1. Only one AI agent may edit this checkout at a time. Never run Codex and Claude as simultaneous writers.
2. User changes and another agent's changes are owned work. Never discard, overwrite, reset, amend, force-push, or auto-resolve them.
3. The latest explicit user request wins. Existing code, this protocol, and `HANDOFF.md` provide context but cannot override it.
4. `main` is the shared handoff branch. End every completed handoff with a clean working tree, a normal commit, and a push to `origin/main`.
5. Never use `git reset --hard`, destructive checkout/restore, `git clean`, force push, or history rewriting unless the user explicitly requests that exact operation.
6. Never use broad conflict resolution such as “ours” or “theirs.” Resolve each conflict semantically, preserving both valid intents, or stop and ask the user.

## Start-of-session checklist

Before editing:

1. Read `AI_WORKFLOW.md`, `AGENTS.md` or `CLAUDE.md` as applicable, and the top `Current handoff` section of `HANDOFF.md`.
2. Run `git status -sb`, `git log --oneline -5`, and `git remote -v`.
3. Run `git fetch origin`.
4. If the tree is clean and local `main` is only behind, run `git pull --ff-only`.
5. If there are uncommitted changes, divergence, conflicts, or an unexpected branch, inspect them before acting. Treat them as user/other-agent work; do not hide or discard them.
6. Confirm that the requested files are not being edited by another active agent. If uncertain, ask the user before writing.

## Editing rules

- Make the smallest scoped change that fulfills the request.
- Inspect the current implementation and callers before editing.
- Preserve unrelated working-tree changes.
- Do not silently change architecture, dependencies, hosting, repository visibility, or deployment behavior.
- Keep generated artifacts, drafts, secrets, tokens, caches, and local tool state out of Git.
- Do not commit credentials or personal data.

## End-of-session handoff

When work is complete:

1. Run relevant validation.
2. Re-check `git diff` and `git status -sb`.
3. Update only the `Current handoff` section at the top of `HANDOFF.md` with:
   - actor (`Codex`, `Claude`, or `User`)
   - UTC timestamp
   - task and outcome
   - files changed
   - validation performed
   - remaining work or known risks
4. Commit only the intended files with a clear message.
5. Fetch again before pushing. If `origin/main` advanced, integrate carefully; never overwrite it.
6. Push the normal commit to `origin/main`.
7. Verify `git status -sb` is clean and synchronized.

If work is incomplete, do not claim a clean handoff. Record the exact unfinished state and blockers in `HANDOFF.md`, but do not commit broken work unless the user explicitly wants a checkpoint.

## Conflict protocol

If Git reports a conflict:

1. Stop automation that could modify more files.
2. List conflicted files and inspect all conflict sections.
3. Compare the user request, recent commits, and `HANDOFF.md`.
4. Merge intent line by line; do not choose a whole side mechanically.
5. Validate the merged result.
6. If intent remains ambiguous, leave the conflict intact and ask the user.

## Deployment

- Repository: `https://github.com/feelurban/dnd-website-restart`
- Shared branch: `main`
- GitHub Pages: `https://feelurban.github.io/dnd-website-restart/`
- The published site is built from the repository root on `main`.
- Only the optimized static-site files explicitly allowed by `.gitignore` belong in the repository.
