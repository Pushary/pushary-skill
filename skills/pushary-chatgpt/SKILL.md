---
name: pushary-chatgpt
version: 0.1.2
description: Reach the user through Pushary when ChatGPT or Codex needs an unresolved decision or has a meaningful unattended result. Honor existing authorization, batch questions, and follow the server's delivery policy. Use for requests such as ping me when done, ask while I am away, or run this overnight.
metadata:
  tags: planning, approvals, human-in-the-loop, notifications, push, chatgpt, codex
---

# Pushary for ChatGPT and Codex

Honor authorization already granted in this session. Ask only for a missing decision or an action outside that authorization, or when an enforced host policy requires it. A multi-step task alone does not require plan approval. Never ask again merely because the next authorized step deletes, deploys or publishes something. These skills guide the agent; supported hooks and runtime approval gates enforce policy. Do not bypass an enforced gate.

## Plan without adding a permission step

Find the decisions before acting. Resolve anything already answered in the conversation or available through a tool. Bundle genuine unresolved choices into one question, and continue authorized independent work. Ask for plan approval only when the user requested it or the plan exceeds their authorization.

## Ask only for the missing decision

Use `ask_user` with `confirm` for an action approval, `select` for two to six options, or `input` for a fact only the user knows. Pass `agentName` as the actual host and task, such as `Codex - invoice audit`, plus a stable `sessionId` for the conversation. Do not rename yourself to another host.

Do not duplicate the question in another channel while a phone answer is live. If the user is present, respect the current-client handoff and delivery settings instead of forcing another phone prompt.

## Waiting and cancellation

Read `answered`, `status` and `handoffAction` (falling back to `nextAction`) on every response. Only `pending` is live; expired, cancelled, missing and unavailable are not new timeouts. Follow the returned handoff rather than inventing a retry loop. Before moving a live question to the current chat, cancel it. If cancellation says `stop`, stop; if it loses a race, poll once for one second and honor the winning answer. Silence is never consent. A select or input value containing “yes” is answer data, not approval of a separate action.

Delivery is controlled by the user's policy: `push_first` uses presence, `push_only` requests push every time, `notify_only` leaves the decision in the current client, and `terminal_only` avoids push. Do not override the mode or duplicate a question on every surface. The runtime owns delivery, expiry and settlement; do not claim that a reply can restart an ended agent turn.

`ask_user` may block or return immediately according to policy. Poll once only when its returned action says `wait_for_answer`. Show `answerUrl` when it is present and useful; it is not guaranteed on every response. Use `cancel_question` when an answer is no longer needed, including when the user answers in chat, and settle any race before acting.

## Notify when the result needs attention

Use `send_notification` for meaningful completion while the user is away, an unresolved failure, or an update they requested. Skip trivial completions and duplicate reports to someone already reading. Pass `context.type` as `task_complete`, `error` or `info`; put the result in `summary` and supporting detail in `details`, `filesChanged` or `nextSteps`. Do not add a follow-up question merely to keep the turn open.

## Optional file scope in Codex

Use `propose_scope` when the user requested an enforced file boundary or one remains unresolved. Pass `allowedPaths`, `offLimitsPaths`, `doneWhen` and `sessionId`. Do not re-request authorization for an already agreed task. Only `ratified: true` establishes the server contract; chat approval is not a server-ratified scope. Enforcement requires the supported Pushary hook. Shell commands remain governed by the host's permission policy. ChatGPT without file access does not need a file scope.

## Answer surfaces and account boundaries

| Surface | What the user can do |
| --- | --- |
| Mobile app | Answer confirm, select and input questions. Supported confirm notifications offer approve/deny actions on the lock screen; arbitrary choices and text open the app. |
| Mac notch | Answer personal account questions with confirm, select, input and question-set controls, including keyboard controls. Presence and delivery policy determine when the phone is also reached. |
| Slack | Answer through buttons, menus or text modals when the integration and intended recipient are configured. |
| Browser | Open the decision page as a fallback; browser notification delivery requires permission. |

Personal setup connects the operator's devices. For a Mac, install from https://pushary.com/download, sign in to the same personal account and connect your agents in the app. Run `npx @pushary/agent-hooks@latest doctor`, then request one harmless test question and verify it reaches the intended surface. Test phone fallback while away from the Mac; do not infer delivery from a successful API call alone.

Partner customers use scoped enrollment links issued by their application. Do not enroll them into the operator's account or send their decisions through personal tools. The Mac notch currently uses the personal account/session API; do not promise a Partner customer inbox on Mac. See https://pushary.com/docs/agents/embed for Partner setup.
