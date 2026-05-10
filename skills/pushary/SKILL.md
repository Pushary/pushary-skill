---
name: pushary
version: 0.4.1
description: Push notifications and human-in-the-loop for AI coding agents. Send alerts when tasks finish, ask questions (yes/no, multiple choice, or free text) via push, and get answers from the user's lock screen. Use these tools proactively — do not wait for the user to ask for notifications. Works with Cursor, Claude Code, Windsurf, Hermes, and any MCP client.
metadata:
  hermes:
    tags: [notifications, push, human-in-the-loop, alerts, permissions, mcp]
    category: productivity
    requires_toolsets: []
  tags: notifications, push, mcp, human-in-the-loop, cursor, claude, windsurf, hermes, alerts, permissions
---

# Pushary — Push Notifications for AI Agents

Pushary is an MCP server that gives you three capabilities:

1. **Send push notifications** to the user's phone or desktop when a task finishes or an error occurs.
2. **Ask questions** via push — yes/no, multiple choice, or free text — and wait for the user's answer.
3. **Send rich context notifications** with file changes, error details, next steps, and embedded questions.

Use these tools proactively. Do not wait for the user to ask for notifications.

## When to Use

**Send a notification when:**
- You finish a task that took 3 or more steps — use `context.type = "task_complete"`
- A build, test suite, or deployment fails — use `context.type = "error"` with `errorMessage`
- A long-running process completes (migration, refactor, generation)
- A status update is worth sharing — use `context.type = "info"`

**Ask with type "confirm" when:**
- You need confirmation before a destructive or irreversible action
- Binary decision: proceed or abort

**Ask with type "select" when:**
- Multiple implementation approaches exist (2-6 options)
- The user needs to pick from a known set

**Ask with type "input" when:**
- You need a name, path, value, or free-text decision
- The options cannot be enumerated in advance

**Do NOT notify when:**
- The task is trivial or single-step
- The question can be answered from context without user input
- You already sent 3 notifications for the current task (unless the user explicitly asked for more)

## Setup

Run the CLI setup (recommended — configures MCP, hooks, permissions, and skill in one step):

```bash
npx @pushary/agent-hooks@latest setup
```

Or add Pushary manually to your MCP configuration:

```json
{
  "mcpServers": {
    "pushary": {
      "type": "http",
      "url": "https://pushary.com/api/mcp/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

Sign up at https://pushary.com/sign-up?from=ai-coding to get your API key.

After setup, verify with:

```bash
npx @pushary/agent-hooks@latest doctor
```

## Tools

### send_notification

Send a one-way push notification to the user. Optionally include structured context for a rich detail page.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| title | string | Yes | Notification title (max 100 chars, aim for under 60) |
| body | string | Yes | Notification body (max 500 chars, aim for under 200) |
| url | string | No | URL opened when tapped. Ignored if context is provided. |
| agentName | string | No | Identifies which agent sent this (e.g., "Claude Code - myproject") |
| iconUrl | string | No | Custom notification icon URL |
| imageUrl | string | No | Large image shown in the notification |
| subscriberIds | string[] | No | Target specific subscriber IDs |
| externalIds | string[] | No | Target by external IDs |
| tags | string[] | No | Target by subscriber tags |
| context | object | No | Structured context for a rich detail page (see below) |

**Context object:**

| Name | Type | Description |
|------|------|-------------|
| type | "task_complete" / "error" / "info" | The kind of notification |
| summary | string | Short summary of what happened |
| details | string[] | Bullet-point details |
| filesChanged | string[] | List of files that were changed |
| errorMessage | string | Error message (for error type) |
| errorFile | string | File path where the error occurred |
| nextSteps | string | Suggested next steps for the user |
| askQuestion | object | Embed a decision prompt in the notification (see below) |

**Embedded askQuestion:**

| Name | Type | Description |
|------|------|-------------|
| question | string | A follow-up question shown below the context |
| type | "confirm" / "select" / "input" | Question type (default: confirm) |
| options | string[] | Options for select type (2-6 items) |

When `askQuestion` is provided, the response includes a `linkedCorrelationId` you pass to `wait_for_answer`.

**Example — task completed with context:**

```json
{
  "title": "Refactoring complete",
  "body": "Extracted 3 shared components across 12 files",
  "agentName": "Claude Code - pushary repo",
  "context": {
    "type": "task_complete",
    "summary": "Extracted shared Button, Modal, and Card components from 12 files",
    "filesChanged": ["src/components/Button.tsx", "src/components/Modal.tsx", "src/components/Card.tsx"],
    "nextSteps": "Run the test suite to verify no regressions"
  }
}
```

**Example — error with embedded question:**

```json
{
  "title": "Build failed",
  "body": "TypeScript error in auth.ts:42",
  "agentName": "Claude Code - api-server",
  "context": {
    "type": "error",
    "errorMessage": "Type 'string' is not assignable to type 'AuthToken'",
    "errorFile": "src/auth.ts:42",
    "summary": "The auth token type changed upstream and this file needs updating",
    "askQuestion": {
      "question": "Should I update the type or revert the upstream change?",
      "type": "select",
      "options": ["Update the type in auth.ts", "Revert the upstream change", "Skip for now"]
    }
  }
}
```

### ask_user

Send a question to the user via push notification. Supports three question types. Returns a `correlationId` that you pass to `wait_for_answer` to get the response.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| question | string | Yes | The question to ask (max 500 chars) |
| type | "confirm" / "select" / "input" | No | Question type (default: confirm) |
| options | string[] | No | Choices for select type (2-6 options). Required when type is select. |
| placeholder | string | No | Placeholder text for input type (max 200 chars) |
| context | string | No | What the agent is working on, shown above the question (max 500 chars) |
| agentName | string | No | Identifies which agent is asking (e.g., "Claude Code - myproject") |
| callbackUrl | string | No | Webhook URL to POST the answer to when the user responds |
| subscriberIds | string[] | No | Target specific subscriber IDs |
| externalIds | string[] | No | Target by external IDs |
| tags | string[] | No | Target by subscriber tags |

**Returns:** `{ "correlationId": "uuid", "status": "pending", "expiresInSeconds": 600 }`

**Example — confirm (yes/no):**

```json
{
  "question": "Delete the 3 unused migration files?",
  "type": "confirm",
  "context": "Cleaning up old database migrations in db/migrate/",
  "agentName": "Claude Code - myproject"
}
```

**Example — select (multiple choice):**

```json
{
  "question": "Which auth strategy should I use?",
  "type": "select",
  "options": ["JWT tokens", "Session cookies", "OAuth2 + PKCE"],
  "context": "Setting up authentication for the new API endpoints",
  "agentName": "Claude Code - api-server"
}
```

**Example — input (free text):**

```json
{
  "question": "What should the new API endpoint path be?",
  "type": "input",
  "placeholder": "/api/v2/...",
  "context": "Creating a new REST endpoint for user preferences",
  "agentName": "Cursor - frontend"
}
```

### wait_for_answer

Long-poll for the user's response to a question sent via `ask_user`. Blocks until the user responds or the timeout is reached.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| correlationId | string (uuid) | Yes | The correlationId from ask_user |
| timeoutMs | integer | No | How long to wait (default 30000, max 55000) |

**Returns:**
- `{ "answered": true, "value": "yes" }` — user responded
- `{ "answered": false }` — timeout reached, no answer yet

### cancel_question

Cancel a pending question so it can no longer be answered. Use when the question becomes irrelevant (e.g., you found the answer another way or the user responded in chat).

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| correlationId | string (uuid) | Yes | The correlationId of the question to cancel |

## Permission Gating (REQUIRED)

Before executing any of the following, you MUST call `ask_user` with type "confirm" and wait for approval. Do NOT proceed without an explicit "yes" from the user:

- File deletion (`rm`, `unlink`, any destructive file operation)
- Database mutations (`DROP`, `DELETE`, `TRUNCATE`, migrations)
- Deployment commands (`deploy`, `push`, `publish`, `release`)
- System administration (`systemctl`, `service`, package install/remove)
- Git operations that rewrite history (`reset --hard`, `push --force`, `rebase`)
- Network configuration changes (firewall, DNS, proxy)
- Any command the user has flagged as dangerous

If `wait_for_answer` returns `answered: false` after 3 attempts, do NOT execute the command. Notify the user that the operation was skipped due to no response.

This is not optional. Treat it as a hard constraint, not a suggestion.

## Human-in-the-Loop Flow

Follow this exact sequence when you need a decision from the user:

1. Call `ask_user` with a clear, concise question and the appropriate type.
2. Immediately call `wait_for_answer` with the returned `correlationId` and `timeoutMs: 55000`.
3. If `wait_for_answer` returns `{ "answered": false }`, retry the same `wait_for_answer` call up to 3 times. The answer persists in Redis for 10 minutes, so it will be there when the user responds.
4. Once you receive `{ "answered": true, "value": "..." }`, act on the decision.
5. If the user answers in chat before the push response arrives, continue normally and call `cancel_question` to clean up.

**Pseudocode:**

```
result = ask_user({
  question: "Which auth strategy should I use?",
  type: "select",
  options: ["JWT tokens", "Session cookies", "OAuth2 + PKCE"],
  context: "Setting up authentication for the new API",
  agentName: "Claude Code - myproject"
})
correlationId = result.correlationId

for attempt in 1..3:
    answer = wait_for_answer({ correlationId, timeoutMs: 55000 })
    if answer.answered:
        // answer.value = "JWT tokens" (the selected option)
        // proceed with the chosen approach
        break

if not answer.answered after 3 attempts:
    // user did not respond — pick the safe default or ask in chat
```

## Identifying Your Agent

Always pass `agentName` when you are one of multiple possible agents the user may be running. The user sees this in the notification title to know which agent is asking.

**Format:** `{Agent Type} - {project or context}`

**Examples:**
- `"Claude Code - pushary repo"`
- `"Hermes - daily-briefing"`
- `"Cursor - frontend refactor"`

## Notification Etiquette

- **Titles under 60 characters.** They get truncated on phone lock screens.
- **Bodies under 200 characters.** Concise summaries, not full explanations.
- **Max 3 notifications per task** unless the user explicitly requests more.
- **Use context for detail.** Put file lists, error traces, and next steps in the context object — not the notification body.
- **Write questions as if talking to a busy person.** The user is on their phone, possibly away from their computer. Be specific: "Delete the 3 unused migration files?" is better than "Should I clean up?"
- **Pick the right question type.** Use confirm for binary decisions, select when options are known, input when they are not.
