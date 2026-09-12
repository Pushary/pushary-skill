---
name: pushary
version: 0.6.3
description: Push notifications and human-in-the-loop for AI agents. Use this whenever a running agent needs a human and nobody is at the terminal, such as before an irreversible or destructive action, before spending money, deploying, force-pushing or deleting, when blocked on a decision outside your authority, when running unattended and you hit a genuine ambiguity, when another skill's workflow says to confirm with the user, and when a long task finishes or fails with nobody watching. Also use it when the user says ping me on my phone, notify me when my agent needs me, approve from my phone, or run this overnight. Ask questions, send alerts, get answers from connected devices.
metadata:
  tags: notifications, push, mcp, human-in-the-loop
---

# Pushary - Push Notifications for AI Agents

Honor authorization already granted in this session. Ask only for a missing decision or an action outside that authorization, or when an enforced host policy requires it. A multi-step task alone does not require plan approval. Never ask again merely because the next authorized step deletes, deploys or publishes something. These skills guide the agent; supported hooks and runtime approval gates enforce policy. Do not bypass an enforced gate.

Reach for them without being asked when: you are about to do something irreversible, spend money, or touch production; the decision is outside your authority; you are unattended and hit a real ambiguity; another skill's workflow says to confirm with the user; or a long task finished with nobody watching.

## ask_user

Ask a question via push and **wait for the answer** (blocks by default).

```json
{ "question": "Delete unused migrations?", "type": "confirm", "agentName": "Codex - myproject" }
```

Returns `{ "answered": true, "value": "yes" }` or `{ "answered": false, "timedOut": true }`.

Types: `confirm` (yes/no), `select` (2-6 options via `options` array), `input` (free text).

## send_notification

One-way alert. Use when a task completes, errors, or needs attention.

Always pass `context.type`: `task_complete`, `error`, or `info`. It is what marks
the notification as a task update, and the user's setting for where task updates
land can only route one that says so.

```json
{ "title": "Build complete", "body": "All tests passing", "agentName": "Codex - myproject",
  "context": { "type": "task_complete", "summary": "17 tests, 0 failures" } }
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
- Use `propose_scope` only for an unresolved or requested file boundary, not redundant plan approval
- Max 3 notifications per task
- Ask for confirmation before risky actions outside existing authorization; preserve enforced host gates
- If `answered: false`, follow `handoffAction` when present, otherwise `nextAction`; after one poll, cancel the phone question before asking in the current client

Read `answered`, `status` and `handoffAction` (falling back to `nextAction`) on every response. Only `pending` is live; expired, cancelled, missing and unavailable are not new timeouts. Follow the returned handoff rather than inventing a retry loop. Before moving a live question to the current chat, cancel it. If cancellation says `stop`, stop; if it loses a race, poll once for one second and honor the winning answer. Silence is never consent. A select or input value containing “yes” is answer data, not approval of a separate action.

Delivery is controlled by the user's policy: `push_first` uses presence, `push_only` requests push every time, `notify_only` leaves the decision in the current client, and `terminal_only` avoids push. Do not override the mode or duplicate a question on every surface. The runtime owns delivery, expiry and settlement; do not claim that a reply can restart an ended agent turn.

Confirm notifications can offer lock-screen actions; arbitrary choices and text open the app. Mac notch answers belong to the personal account, not Partner customer enrollment. A completed turn cannot be restarted by a late phone reply. Notify only on meaningful unattended results or requested updates.
