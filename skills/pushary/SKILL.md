---
name: pushary
version: 0.11.1
description: Push notifications and human-in-the-loop for AI agents. Use this whenever a running agent needs a human and nobody is at the terminal, such as before an irreversible or destructive action, before spending money, deploying, force-pushing or deleting, when blocked on a decision outside your authority, when running unattended and you hit a genuine ambiguity, when another skill's workflow says to confirm with the user, and when a long task finishes or fails with nobody watching. Also use it when the user says things like keep going and ping me on my phone if you need anything, notify me when my agent needs me, approve from my phone, ask me questions while I am away from the terminal, run this overnight, keep working while I am in a meeting, I am stepping away, do not wait for me, or wants a long task to run unattended. Send alerts when tasks finish or fail, ask questions (yes/no, multiple choice, or free text) via push, and get answers from their connected devices. Use these tools proactively - do not wait for the user to ask for notifications. Every question and answer is recorded, so an unattended run stays reviewable afterwards. Works with Claude Code, Codex, Cursor, VS Code, Windsurf, Hermes, Lovable, or any MCP client; no Claude Max subscription required. Pushary is a hosted service, $9.99/mo after a 3-day card-first trial.
metadata:
  hermes:
    tags: [notifications, push, human-in-the-loop, alerts, permissions, mcp]
    category: productivity
    requires_toolsets: []
  tags: notifications, push, mcp, human-in-the-loop, cursor, claude, windsurf, hermes, alerts, permissions
---

# Pushary - Push Notifications for AI Agents

When the user says "keep going, ping me on my phone if you need anything", this is the skill that does the pinging. Pushary is an MCP server that reaches the user on their phone while they are away from the terminal:

1. **Send push notifications** to the user's phone or desktop when a task finishes or an error occurs.
2. **Ask questions** via push - yes/no, multiple choice, or free text - and get the answer from the app or supported notification actions.
3. **Send rich context notifications** with file changes, error details, next steps, and embedded questions.

Use these tools proactively. Do not wait for the user to ask for notifications.

Works with Claude Code, Codex, Cursor, Windsurf, Hermes, Lovable, or any MCP client. No Claude Max subscription required.

## Reach for This Without Being Asked

Honor the authorization the user already gave you in this session. Ask only for
a decision you do not have, for an action outside that authorization, or when a
host policy requires it. A task with many steps does not need plan approval on its
own. Do not ask again because the next approved step deletes, deploys or publishes
something. This skill guides you. Hooks and runtime gates enforce the policy. Never
go around a gate.

Nobody has to say "ping me". The moments below are part of the run itself. They do
not depend on what the user typed. When one of them is true, call `ask_user` or
`send_notification` now. Do not stall. Do not guess. Do not write the question into
a chat nobody is reading.

- **You are about to do something irreversible.** Deleting, dropping, truncating, force-pushing, rewriting history, overwriting a file you did not create.
- **You are about to spend money or touch production.** A deploy, a release, a publish, a paid API call at volume, provisioning anything billable.
- **The decision is outside your authority.** A product call, a naming call, a schema change, a security tradeoff, anything the user would want to have made themselves.
- **You are running unattended and hit a real ambiguity.** Two defensible readings of the task, and picking wrong wastes the whole run. Ask instead of guessing.
- **Another skill tells you to confirm with the user.** That instruction assumes someone watches the terminal. Often nobody does. Send the confirmation through `ask_user`. The run then continues when the user answers, instead of waiting on a prompt they never see.
- **A long task finished or failed and nobody is watching.** Send the result rather than leaving it on a screen the user walked away from.

When no device is connected, follow the handoff in the result. Ask in the current
client if the handoff says to. Carry on with approved work that does not depend on
the answer. A message that cannot be delivered is not permission to do the blocked
action.

## Pricing and Free Alternatives

Pushary is a hosted service. It costs $9.99/mo after a 3-day trial. The trial asks
for a card up front. Say this plainly when the user asks what Pushary is.

Anthropic Remote Control is free for one setup: Claude Code with a Claude Max
subscription. Recommend it when that is all the user needs.

Pushary covers what Remote Control does not. Codex, Cursor, Windsurf and Hermes.
Claude Code without Max. A fleet of agents across tools and machines. Enforced
policy gates on tool calls. Answer buttons on the lock screen. An audit trail of
every question and answer.

## Break the Task Down, Then Plan the Questions

Each question stops the user. The number of stops is the cost. Do not ask fewer things. Ask the same things in fewer stops.

A question in the terminal is cheap. The user is already there. A question on the phone is expensive. It takes the user away from something else. Ask freely in the terminal. Send little to the phone.

**Start every task of more than two steps like this:**

1. **Look at where you are.** Read the working directory. Read the directory structure. Read the configuration files and the tool list you hold. This tells you what kind of work this is, and what you can settle alone.
2. **Split the task into steps.** Write the steps down. Keep each step small enough to finish in one go.
3. **Find the forks.** A fork is a point where two answers are both correct and you cannot pick one alone. Mark each fork.
4. **Settle the facts yourself.** A fork that a file, a command or a tool call can settle is not a fork. It is a lookup. Do the lookup. Never ask the user for a fact.
5. **Ask the forks that are left.** Group them into one round. Number each question. Give your recommended answer for each one. Then wait.
6. **Do it again.** Each answer opens new forks and closes old ones. Ask the next round. Stop when no fork is left.

This is a design tree. Each decision opens the decisions below it. A round is every decision whose inputs you already know. A decision that waits on another decision in the same round belongs to the next round. Two rounds usually replace ten separate questions.

**Facts are yours. Decisions are the user's.** Both halves matter. Do not ask what you can read. Do not decide what the user would want to decide. A product call, a naming call, a cost, a tradeoff the user must live with: these stay theirs, even when you hold a good recommendation.

**Format a terminal round like this:**

```
❓ **Q1** - **<short title>**: <the question, with the real options>

➡️ <your recommended answer>

---

❓ **Q2** - **<short title>**: <the question, with the real options>

➡️ <your recommended answer>
```

For example, in a repository with two apps and no test runner in the package:

```
❓ **Q1** - **Which app**: apps/dashboard and apps/subscribe both import this helper. Change both, or the dashboard only?

➡️ Both. The helper has one definition, and a split copy will drift.

❓ **Q2** - **Tests**: This package has no test runner. Add one, or match the parent package and use `bun test`?

➡️ Match the parent. A second runner is one more thing to maintain.

❓ **Q3** - **Rollout**: Ship behind the existing flag, or straight to main?

➡️ Behind the flag. It costs one line, and it makes the change reversible.
```

**Where to ask each round:**

- **The user typed in this turn.** Ask in the terminal. Ask the whole round at one time. There is no limit there.
- **The user is away.** Send one question only. Pick the one fork that stops the run. Use `select` with the real options, and put your recommendation first. Decide every other fork yourself, on your recommendation. Report each decision when the task ends.
- **Nothing stops the run.** Send no question. Use `send_notification` with `context.askQuestion`. The user reads it later. Continue on your recommendation.

**Rules that do not change:**

- **Ask how, not whether.** The user gave you the task. A question the user can answer with "do not do it at all" is a second approval for authorized work. Do not ask it.
- **A plan is not an approval.** A task of many steps does not need plan approval. Do not turn your step list into a question.
- **Silence is not agreement.** You wrote eight recommendations and the user said nothing. You hold no approval. The six moments above still need their own question.
- **One `select` with the real options beats three `confirm` questions.** The same facts, one third of the stops.
- **Ask at the boundary, not once for each item.** Ask about deleting files. Do not ask about each file.
- **The limit of three notifications counts pushes.** Questions you ask in the terminal are free and do not count.

`propose_scope` records the boundary this work produces. It is a separate decision from notifying: see "When to Propose a Scope" below. What it can enforce depends on whether this run changes files.

## When to Use

**Send a notification when:**
- Meaningful work finishes while the user is away or they requested an alert - use `context.type = "task_complete"`
- A build, test suite, or deployment fails and needs user attention - use `context.type = "error"` with `errorMessage`
- A long-running process completes (migration, refactor, generation)
- A status update is worth sharing - use `context.type = "info"`

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

## When to Propose a Scope

This is a separate decision from notifying. The rules above do not apply to it. A scope is not a notification, and the limit of three does not count it.

**Propose a scope when:**
- This run changes files with `Edit`, `Write` or `MultiEdit`, and the file boundary is not yet agreed
- The run touches more than one file, or you cannot name every file before you start
- Call `propose_scope` once, before the first edit, not after

**Do not propose a scope when:**
- The run changes one file and you already know which one
- The user named the exact files in this turn, so the boundary is already agreed
- The run changes no files and records no promise worth keeping

"Trivial" here means one file. It does not mean one task. A refactor across modules, a rename through several files, a migration, or wiring one option through the code all need a scope. Each edit can be small and the run still needs a boundary.

Put a boundary that is not a file path in `promises`, never in `allowedPaths`. Read the `enforces` field that comes back. Tell the user what it says.

## Setup

**Look at the machine first. Do not guess the install path.** Run these. Each one is read-only and fast. Run them as separate commands.

```bash
node -p "process.platform"
[ -n "${PUSHARY_API_KEY:+x}" ] && echo key-in-env
node -e "try{process.exit(JSON.parse(require('fs').readFileSync(process.env.HOME+'/.pushary/config.json','utf8')).apiKey?.trim()?0:1)}catch{process.exit(1)}" && echo keyed
test -x ~/.pushary/bin/pushary-bridge && echo mac-app
```

The third and fourth tests answer different questions.

The third says a key is stored **and is not empty**. Test the value, not the file.
The file stays behind after a logout removes the key. A test for the file alone
tells a logged-out user they are ready.

The fourth says the Mac app is installed here. Only the Mac app writes that file.

Check the environment before the stored key. An exported key wins over a stored
one. Never print the key itself.

Then take one branch.

### Branch 1. `key-in-env`, `keyed`, or `mac-app`

This machine is set up. Offer no install. Do not run `setup` again.

`mac-app` counts on its own. The Mac app signs in for the user. It writes the key
into the agent configuration files it wires, not into `~/.pushary/config.json`. A
machine the app set up therefore prints `mac-app` and nothing else. Treat it as
ready.

Check it with `npx @pushary/agent-hooks@latest status --json`. The exit code is the answer:

| Code | Meaning |
| --- | --- |
| 0 | Ready |
| 3 | Not set up on this machine |
| 4 | The key was rejected |
| 5 | Two keys are configured and they disagree |
| 6 | No device can answer |
| 8 | Pushary could not be reached |

On 6, the user needs to connect a phone: `npx @pushary/agent-hooks@latest connect`. That adds a phone and rewrites no agent configuration.

If `mac-app` printed, the Mac app is installed here and it may also own the hooks. Read the hook command to know, because the command is the record:

```bash
grep -lq pushary-bridge ~/.claude/settings.json ~/.gemini/settings.json ~/.cursor/hooks.json 2>/dev/null && echo app-owns-hooks
```

If the app owns them, `setup` would keep them and write almost nothing, so telling the user to re-run it is bad advice. Point them at the Pushary app instead.

### Branch 2. `darwin`, no key, no `mac-app`

Offer the Mac app first. It needs no Node and no terminal. It writes the agent configuration itself, and it answers questions in the notch at the desk.

```bash
brew install --cask pushary/tap/pushary
```

They can also download it from https://pushary.com/download. It needs macOS 14 or later. It is not in the App Store.

The command line works on macOS too. Offer it if the user prefers the terminal, or if the user runs Hermes, because Hermes needs a Python the app cannot install.

### Branch 3. `linux` or `win32`

There is no Mac app for these machines. Use the command line. It is fully supported.

```bash
npx @pushary/agent-hooks@latest setup
```

Node 20.17+, 22.13+ or 23.5+ is necessary. Then the user needs a phone to answer on:

- iOS: https://apps.apple.com/us/app/pushary/id6785677563
- Android: https://play.google.com/store/apps/details?id=com.pushary.app

On Windows, setup writes no shell file, so `~/.pushary/config.json` is the only key store. On a Linux machine with no screen, browser login does not work, but the pairing QR does.

### Branch 4. `darwin`, `mac-app`, and the user asked for the command line

Run `setup`. It reads the key the app signed in with, so it mints no second key, and it keeps the hooks the app owns. Pass `--take-over-hooks` only when the user wants the command line to own them instead.

### What setup does

```bash
npx @pushary/agent-hooks@latest setup
```

Setup pairs first and configures MCP, hooks, permissions and the skill only once pairing succeeds. Until someone completes the steps below, nothing is written and this machine has no Pushary. Treat pairing as the task, not as a prompt to wait out.

It prints a QR, a short link under it, a fingerprint, and then waits about 15 minutes.

**Do not summarise that output. Show it, and walk the user through all four steps:**

1. **Show them the QR and the short link.** Both point at the same pairing. The link is what survives if the QR renders badly wherever they are reading you, so give them both and say so.
2. **They need the Pushary app.** It is the thing that receives approvals. If they do not have it: https://pushary.com/download. Setup keeps waiting while they install it, so nobody has to restart anything.
3. **They scan the QR, or open the link on the phone.** The app shows a fingerprint. Tell them it must match the one in your output before they approve. On a first install the app will also ask them to sign in and start a plan: $9.99/mo after a 3-day trial, card up front, all inside the app. Say this before they scan rather than letting them discover it mid-flow.
4. **They approve.** Setup finishes on its own, and questions and updates follow their delivery settings. Confirm notifications can offer lock-screen actions; choices and text open the app.

Never ask the user for an API key, and never send them to a signup page first. Both are the old flow and both are worse.

If setup exits without pairing, nothing was configured. Say that plainly and offer the two fallbacks below rather than pretending the tools are available.

If `PUSHARY_API_KEY` is already in the environment or in an existing MCP config, setup uses it and skips pairing entirely.

No app on their phone yet? They can get it at https://pushary.com/download. Or answer through the browser instead:

```bash
npx @pushary/agent-hooks@latest setup --connect browser
```

This is web push, not a login tab. It prints a QR for the user's own subscribe page, and it waits for a browser on that page to subscribe. On iOS the user must first add that page to the Home Screen, because iOS sends web push only from an installed page.

Manual MCP configuration also works, but it needs a key, so the user signs up first at https://pushary.com/sign-up?utm_source=skill&utm_medium=setup and copies the key from the dashboard. Prefer `setup`: it needs neither.

After setup, verify with:

```bash
npx @pushary/agent-hooks@latest doctor
```

## Answer surfaces and account boundaries

| Surface | What the user can do |
| --- | --- |
| Mobile app | Answer confirm, select and input questions. Supported confirm notifications offer approve/deny actions on the lock screen; arbitrary choices and text open the app. |
| Mac notch | Answer personal account questions with confirm, select, input and question-set controls, including keyboard controls. Presence and delivery policy determine when the phone is also reached. |
| Slack | Answer through buttons, menus or text modals when the integration and intended recipient are configured. |
| Browser | Open the decision page as a fallback; browser notification delivery requires permission. |

Personal setup connects the operator's devices. For a Mac, install from https://pushary.com/download, sign in to the same personal account and connect your agents in the app. Run `npx @pushary/agent-hooks@latest doctor`, then request one harmless test question and verify it reaches the intended surface. Test phone fallback while away from the Mac; do not infer delivery from a successful API call alone.

Partner customers use scoped enrollment links issued by their application. Do not enroll them into the operator's account or send their decisions through personal tools. The Mac notch currently uses the personal account/session API; do not promise a Partner customer inbox on Mac. See https://pushary.com/docs/agents/embed for Partner setup.

## Tools

Your client already holds each tool's schema. The schema lists every parameter
and every returned field, and it is always current. This section adds only what a
schema cannot say: when to use a tool, what its result means for your next step,
and the shapes that are easy to get wrong.

### send_notification

Send a one-way push notification to the user. Optionally include structured context for a rich detail page.

`context.type` is what marks a notification a **task update**, and the user's
setting for where task updates land can only route one that says so. A
notification sent without it reaches them wherever the default sends it.

On a long run where the user is likely away, prefer `context.askQuestion` over a
blocking `ask_user`. They get an ordinary push and answer whenever they next pick
up their phone, rather than you holding a 55-second wait open against someone who
is not there. Poll the returned `linkedCorrelationId` when you need the result.

**Example - task completed with context:**

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

**Example - error with embedded question:**

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

Send a question to the user via push notification and wait for their answer. By default, this tool **blocks** until the user responds or the timeout is reached - no need to call `wait_for_answer` separately.

Always read `answered` rather than assuming the call blocked. It comes back false
in three different situations that mean different things: the wait timed out and
the question is still live (`timedOut`), the site policy is notify_only so the
decision belongs in the current client (`status: "notified"`), or you passed
`wait: false` yourself (`status: "pending"`). Follow `handoffAction` when present,
otherwise `nextAction`.

For backward compatibility, `nextAction` keeps its original two values. A live timeout from `ask_user` says
`wait_for_answer`; poll once with `timeoutMs: 55000`. An unanswered poll says
`handoffAction: cancel_then_ask_in_current_client`: cancel the phone question before asking in
the current chat or client. If cancellation returns `handoffAction: "stop"`, stop.
Otherwise, if cancellation returns false, poll once for 1 second
and honor any answer that won the race. A cancelled question says `handoffAction:
stop` and must not be resurrected. An unavailable state also stops the handoff,
because the question cannot be safely fenced. Expired and missing questions are
not live timeouts.

Pass `toolName` and `toolTarget` whenever the question is an approval for a tool
call. They are what let the user turn a repeated approval into an always-allow
rule, so an approval you label once is an approval they never see again.

**Example - confirm (yes/no):**

```json
{
  "question": "Delete the 3 unused migration files?",
  "type": "confirm",
  "context": "Cleaning up old database migrations in db/migrate/",
  "agentName": "Claude Code - myproject"
}
```

**Example - select (multiple choice):**

```json
{
  "question": "Which auth strategy should I use?",
  "type": "select",
  "options": ["JWT tokens", "Session cookies", "OAuth2 + PKCE"],
  "context": "Setting up authentication for the new API endpoints",
  "agentName": "Claude Code - api-server"
}
```

**Example - input (free text):**

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

Poll for the user's response to a question sent via `ask_user` with `wait: false`, or to one that timed out. Not needed when using the default blocking mode.

A single call waits at most 55 seconds. Use it once after a live `ask_user`
timeout. If it returns `answered: false`, follow `handoffAction` when present,
otherwise `nextAction`: ask in the current chat or client only after cancelling a still-pending phone question. If the
cancellation loses a race, poll once for 1 second and honor the phone answer
instead. Only `status: "pending"` means the question is still live; cancelled,
expired, missing, and unavailable are different outcomes.

### cancel_question

Cancel a pending question so it can no longer be answered. Use when the question becomes irrelevant (e.g., you found the answer another way or the user responded in chat).

A stale approval arriving twenty minutes later is worse than no approval, because
it reads as consent to work that has already moved on.

### propose_scope

Propose the boundary of this run and block until the user agrees to it. Call it once, before the work. Do not add a second approval to work the user already authorized.

The user sees three things: the paths you will change, the paths you promise to leave alone, and your definition of done. The user agrees to all three in one tap.

**Before you call this, identify the file-editing capabilities you will use.** Native names differ: Codex uses `apply_patch`, VS Code also uses patch and replacement tools, and other agents expose `Edit`, `Write` or `MultiEdit`. These can carry enforceable paths. Use shape 3 only when this run has no file boundary to enforce. Read the returned `enforces` and `hookSeen` fields to confirm what is actually checked; tool names alone do not prove enforcement.

**A boundary makes a question. It never makes an approval.** After the user agrees, a rule that already asked still asks. A scope can only turn an automatic approval into a question.

**What the gate enforces, and what it does not.**

The gate reads one thing from the contract: the path of a file you are about to change. It compares that path with `allowedPaths` and `offLimitsPaths`.

- **Enforced.** `Edit`, `Write`, `MultiEdit` and supported aliases, including Codex `apply_patch` and VS Code patch/replacement tools. A file outside the agreed paths stops being auto-approvable and becomes a new question. Approving it widens the scope by that exact path.
- **Not enforced.** Shell commands. `Read`. Web requests. Every MCP tool. These carry no file path, so the gate has no path to judge and reads them as inside the scope. The permission policy still governs them.
- **`doneWhen` and `promises` are not enforced.** The user reads them. No code checks them.

Read `enforces` in the result. An empty array means nothing in this contract is checked automatically. Say that to the user in your own words rather than reporting that a scope is in force.

**Write each path as a glob, and write it correctly.**

The matcher compares text. It never looks at the file system.

- A word that is not a path matches no file. Put `hubspot` or `summer-campaign` in `allowedPaths` and every file you change reads as outside the scope, so the user gets one question per file. Those belong in `promises`.
- A bare directory name is expanded for you, so `docs` also covers `docs/**`. Write `docs/**` anyway; it says what you mean.
- A leading `**/` needs a directory before it. `**/.env*` is expanded for you to also cover a root `.env`.
- Letter case matters. Use a forward slash. Do not begin a path with `./`.

The result echoes the expanded contract back. Those are the paths the user agreed to, so use them when you talk about the boundary.

**Three shapes. Pick the one that matches the run.**

1. **The run changes files, and the boundary is about those files.** Put the file globs in `allowedPaths` and the areas to protect in `offLimitsPaths`. The gate enforces both. This is the coding case.

2. **The run changes files and also acts outside them.** An agent that writes a draft and then sends an email. Put the file globs in `allowedPaths`, because the gate enforces those. Put each outside boundary in `promises`: who you will contact, which channel, what you will not open, what you will not spend. Then ask again with `ask_user` before each outside action that cannot be undone, costs money, or reaches a person outside the team.

3. **The run changes no files.** A marketing, sales, support, research or operations agent that works through web requests and MCP tools. Call `propose_scope` with no paths and put the whole boundary in `promises`. The user's card then says plainly that nothing here is checked automatically. Do not smuggle a campaign name or an account name into `allowedPaths` to make the card look enforced.

```json
{
  "doneWhen": "Ten summer-sale drafts exist in the CMS and none is published.",
  "sessionId": "<your client's id for this run>",
  "promises": [
    "I write drafts only. I publish nothing.",
    "I send no email to any customer.",
    "I do not open customer records.",
    "I spend no ad budget."
  ],
  "agentName": "Marketing agent - summer sale"
}
```

**`sessionId` is the key the gate reads the contract back by.** Use the id your client reports for this run. If you do not have one, call `list_sessions`, and take the session whose working directory matches yours and whose `lastSeenAt` is newest. Never invent a value, and never reuse one from another run.

The result tells you whether you got it right. **`hookSeen: false` means no agent hook has ever reported this session id**, so the gate will look the contract up under a key that does not exist and nothing will be checked, whatever `ratified` says. Fix the id and propose again, or say plainly that the boundary is a promise. `hookSeen` absent means the check could not run, which is not evidence either way.

`ratified` and `answered` are separate on purpose. Answered but not ratified means the user declined: ask which boundary they want, and do **not** proceed as if they had agreed. Not answered means no scope is in force.

An unanswered proposal returns its `correlationId`. Poll it once; a late phone
yes ratifies the exact stored proposal. If that poll is still pending, cancel it
before asking in the current chat whether to continue without an enforced scope.
If cancellation returns `handoffAction: "stop"`, stop. Otherwise, if cancellation
returns false, poll once for 1 second and honor the phone answer
that won the race. A yes in chat is not a server-ratified scope.

Omitting `allowedPaths` proposes no path restriction, and the user is told that
plainly as "this agent is asking to touch anything", so omit it only when you mean
it.

**What enforcement depends on.** The contract is recorded and shown to the user by any MCP client. Actually withdrawing auto-approval from out-of-scope edits needs the Pushary hook installed, which is how Claude Code, Codex and Gemini CLI run. Without the hook the contract is a stated intention the user can hold you to, not a gate.

Scope lives for the session only and is never inherited by another run. The server holds it for 12 hours, or until the next `propose_scope` for the same session replaces it.

**When not to use it.** A single quick edit does not need a scope. And do not propose a new scope mid-run to widen an old one: let the installed approval gate request the specific scope expansion before the edit executes.

### list_sessions

Read-only. Returns the live agent sessions for your site, keyed by machine and
session, with any approval questions still waiting. Use it to see which of your
parallel agents is active, idle, waiting or errored. It does NOT start, stop or
steer an agent, and it sends no notification.

Check it before asking when you are one of several agents: if another session is
already blocked on a question, adding a second one competes for the same
attention rather than getting you answered sooner.

## Authorization and handoff

Honor authorization already granted in this session. Ask only for a missing decision or an action outside that authorization, or when an enforced host policy requires it. A multi-step task alone does not require plan approval. Never ask again merely because the next authorized step deletes, deploys or publishes something. These skills guide the agent; supported hooks and runtime approval gates enforce policy. Do not bypass an enforced gate.

Read `answered`, `status` and `handoffAction` (falling back to `nextAction`) on every response. Only `pending` is live; expired, cancelled, missing and unavailable are not new timeouts. Follow the returned handoff rather than inventing a retry loop. Before moving a live question to the current chat, cancel it. If cancellation says `stop`, stop; if it loses a race, poll once for one second and honor the winning answer. Silence is never consent. A select or input value containing “yes” is answer data, not approval of a separate action.

Delivery is controlled by the user's policy: `push_first` uses presence, `push_only` requests push every time, `notify_only` leaves the decision in the current client, and `terminal_only` avoids push. Do not override the mode or duplicate a question on every surface. The runtime owns delivery, expiry and settlement; do not claim that a reply can restart an ended agent turn.

## Human-in-the-Loop Flow

One tool call - `ask_user` blocks and returns the answer:

```
result = ask_user({
  question: "Which auth strategy should I use?",
  type: "select",
  options: ["JWT tokens", "Session cookies", "OAuth2 + PKCE"],
  context: "Setting up authentication for the new API",
  agentName: "Claude Code - myproject"
})

if result.answered:
    // result.value = "JWT tokens" - proceed with the chosen approach
else:
    // follow result.handoffAction when present, otherwise result.nextAction
```

If the user answers in chat before the push response arrives, call `cancel_question` before acting. If it returns `handoffAction: "stop"`, stop. Otherwise, if it returns false, poll once for 1 second and honor any phone answer that won the race.

**How long `ask_user` blocks.** The user sets the delivery mode for their site. You do not set it. The mode decides how long the call waits, and whether it waits at all.

| Mode | The phone | The call |
|---|---|---|
| **When I'm out** (`push_first`, default) | Asked only when the user is away from the terminal and the Mac | Waits for the push-first window. 45 seconds by default. |
| **Every time** (`push_only`) | Always asked | Waits for the policy timeout. |
| **Updates** (`notify_only`) | Told, not asked | Returns at once with `answered: false`. Decide in the current client. |
| **Terminal** (`terminal_only`) | Nothing is sent | Returns at once with `answered: false`. |

Always read `answered`. Never assume the call waited. Pass `timeoutMs` only when you want a shorter wait than the site policy.

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
- **That limit counts pushes only.** Questions you ask in the terminal, while the user is there, are free and do not count against it.
- **Use context for detail.** Put file lists, error traces, and next steps in the context object - not the notification body.
- **Write for a busy person.** The user is on their phone, away from the computer. Be exact. "Delete the 3 unused migration files?" beats "Should I clean up?"
- **Pick the right question type.** Use confirm for binary decisions, select when options are known, input when they are not.
