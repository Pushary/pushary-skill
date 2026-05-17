---
name: pushary
version: 0.4.1
description: Push notifications for AI agents. Ask questions, send alerts, get answers from user's phone.
metadata:
  tags: notifications, push, mcp, human-in-the-loop
---

# Pushary — Push Notifications for AI Agents

Three tools. Use proactively — don't wait for the user to ask.

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

## Rules

- `agentName` format: `"{Agent} - {project}"` (e.g., `"Cursor - api-server"`)
- Max 3 notifications per task
- MUST `ask_user` type "confirm" before destructive operations (delete, deploy, force push)
- If `answered: false`, do NOT execute — notify user it was skipped
