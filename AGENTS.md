# Codex project instructions

Before any task, read and follow `AI_WORKFLOW.md` and the `Current handoff` section at the top of `HANDOFF.md`.

These requirements are mandatory:

- Treat user and Claude changes as owned work; never overwrite or discard them.
- Use the start-of-session Git checks in `AI_WORKFLOW.md` before editing.
- Only one AI agent may write to this checkout at a time.
- Finish completed work with validation, an updated handoff, a normal commit, and a push to `origin/main` when authorized by the user's request.
- Never rewrite shared history or force-push unless the user explicitly requests that exact action.
- If local and remote work diverge or intent is ambiguous, stop and ask instead of guessing.

Project implementation guidance may also exist in `CLAUDE.md`; follow it when it does not conflict with the user request or the shared workflow.
