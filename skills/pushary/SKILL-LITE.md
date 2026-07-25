---
name: pushary
version: 0.5.0
description: Push notifications for AI agents. Ask questions, send alerts, get answers from user's phone.
metadata:
  tags: notifications, push, mcp, human-in-the-loop
---

# Pushary - Push Notifications for AI Agents

Three tools. Use proactively - don't wait for the user to ask.

## ask_user

Ask a question via push and **wait for the answer** (blocks by default).

```json
{ "question": "Delete unused migrations?", "type": "confirm", "agentName": "Codex - myproject" }
```

Returns `{ "answered": true, "value": "yes" }` or `{ "answered": false, "timedOut": true }`.

Types: `confirm` (yes/no), `select` (2-6 options via `options` array), `input` (free text).

## send_notification

One-way alert. Use when a task completes, errors, or needs attention.

```json
{ "title": "Build complete", "body": "All tests passing", "agentName": "Codex - myproject" }
```

## cancel_question

Cancel a pending question: `{ "correlationId": "..." }`

## propose_scope

Agree the boundary of a multi-step run up front, once: `{ "doneWhen": "tests pass", "sessionId": "...", "allowedPaths": ["src/**"], "offLimitsPaths": ["**/.env*"] }`

Blocks until the user answers. `ratified: true` means it is live, and after that a file outside the scope becomes a "widen scope?" question instead of a silent approval, so the user is asked once per boundary rather than once per file. Globs only; shell commands stay governed by the permission policy. Enforcement needs the Pushary hook (`@pushary/agent-hooks` 0.59.0+); without it the contract is recorded but not gated.

## list_sessions

Read-only: see your live agent sessions and pending questions (no notification sent). Useful to check if a parallel session is blocked.

## Rules

- `agentName` format: `"{Agent} - {project}"` (e.g., `"Cursor - api-server"`)
- `propose_scope` once at the start of a multi-step run, never for a single edit, never mid-run to widen
- Max 3 notifications per task
- MUST `ask_user` type "confirm" before destructive operations (delete, deploy, force push)
- If `answered: false`, do NOT execute - notify user it was skipped
