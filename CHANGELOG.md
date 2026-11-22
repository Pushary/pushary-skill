# Changelog

Two plugins ship from this directory, each with its own version: the Claude Code plugin in `.claude-plugin/` and the Codex plugin in `.codex-plugin/`. An installed plugin is updated only when its version changes.

## Claude Code plugin

### 0.2.11

- `pushary-human-in-the-loop` skill 0.1.4, from 0.1.3: the skill no longer says a REST ask that returns `status: "stopped"` sent nothing. A stop that lands after the question was sent closes that question. Either way no question is left to poll, and the agent stops the task as before.

### 0.2.10

- `pushary` skill 0.13.7, from 0.13.6, and `pushary-human-in-the-loop` skill 0.1.3, from 0.1.2: every Pushary command they give is written `npx -y pushary@latest …`, and `npx.cmd -y pushary@latest …` in PowerShell, so npm installs the CLI without stopping to ask "Ok to proceed? (y)". What an agent passes and how it reads answers are unchanged.

### 0.2.9

- `pushary` skill 0.13.6, from 0.13.5: a risky step, such as a recursive delete, a force-push or a deploy, is no longer described as deny-only on the phone. The Pushary app approves one from 1.0.66 after Face ID or the phone's passcode. The lock screen, an older app and a workspace that requires the keyboard for risky steps still offer only deny and `defer` ("Handle on your machine"). What an agent passes and how it reads `defer` are unchanged.

### 0.2.8

- `pushary` skill 0.13.5, from 0.13.4: on a `select` question, agents pass `allowOther: true` unless their options cover every answer, so you can type your own reply instead of picking the closest option. The Other box shows on the pushary.com decision page and in the Android app from 1.0.64; the iOS app gets it with 1.0.65. Other places you answer show the options as before.

### 0.2.7

- The plugin runs the Pushary hook before MCP tools (`mcp__.*`) and before `WebFetch`, `Task` and `Agent`, as `npx pushary@latest setup` does. The kill switch and standing denies now stop those calls in bypass, auto and allowlist mode too, and an approval route can hold an MCP call.
- That coverage has a cost: every MCP, `WebFetch`, `Task` and `Agent` call now starts the hook through `npx`, which adds about 2 seconds per call when npx has the package cached and about 8 seconds when it does not. The `WebFetch`, `Task` and `Agent` entry has a 30 second timeout: it only refuses and never waits for you, and 30 seconds leaves room for a cold `npx` start, which happens on the first call after each CLI release. A hook that runs out of time lets the call through, so a shorter timeout could skip the kill switch on that call. The MCP entry keeps 600 seconds, because an approval route can hold an MCP call.
- The plugin carries only the `pushary` and `pushary-human-in-the-loop` skills. The Hermes, ChatGPT and Cowork skills in `skills/` are for those hosts and no longer load in Claude Code. The Cowork one says questions close after 10 minutes, which is not true for a Claude Code key.
- `pushary` skill 0.13.4, from 0.13.1: one `ask_user` or `wait_for_answer` call waits at most 48 seconds; "call me if you get stuck" is asked with `ask_user`, and Pushary phones only when the reply includes `phoneCall`; the agent tells you when a call rings in your own time (`ringsAtLocal`), not the UTC `ringsAt`; a machine with no phone pairs with `npx pushary@latest pair`, run in the background.
- `pushary-human-in-the-loop` skill 0.1.2, from 0.1.0: MCP calls wait at most 48 seconds and REST waits 55; an agent runs setup in the background and keeps it running while the phone pairs.

## Codex plugin

### 0.1.9

- `pushary-human-in-the-loop` skill 0.1.4, from 0.1.3: the skill no longer says a REST ask that returns `status: "stopped"` sent nothing. A stop that lands after the question was sent closes that question. Either way no question is left to poll, and the agent stops the task as before.

### 0.1.8

- `pushary` skill 0.13.7, from 0.13.6, `pushary-human-in-the-loop` skill 0.1.3, from 0.1.2, and `hermes` 0.9.2, from 0.9.1: every Pushary command they give is written `npx -y pushary@latest …`, and `npx.cmd -y pushary@latest …` in PowerShell, so npm installs the CLI without stopping to ask "Ok to proceed? (y)". What an agent passes and how it reads answers are unchanged.

### 0.1.7

- `pushary` skill 0.13.6, from 0.13.5: a risky step, such as a recursive delete, a force-push or a deploy, is no longer described as deny-only on the phone. The Pushary app approves one from 1.0.66 after Face ID or the phone's passcode. The lock screen, an older app and a workspace that requires the keyboard for risky steps still offer only deny and `defer` ("Handle on your machine"). What an agent passes and how it reads `defer` are unchanged.

### 0.1.6

- `pushary` skill 0.13.5, from 0.13.4: on a `select` question, agents pass `allowOther: true` unless their options cover every answer, so you can type your own reply instead of picking the closest option. The Other box shows on the pushary.com decision page and in the Android app from 1.0.64; the iOS app gets it with 1.0.65. Other places you answer show the options as before.

### 0.1.5

- `pushary` skill 0.13.4, from 0.13.0:
  - One `ask_user` or `wait_for_answer` call waits at most 48 seconds. It said 55.
  - "Call me if you get stuck" is asked with `ask_user`. Pushary phones only when the reply includes `phoneCall`, and the agent does not promise a call otherwise. It tells you when the call rings in your own time (`ringsAtLocal`), not the UTC `ringsAt`.
  - The agent follows the steps `pushary status` prints for each `status --json` exit code, and pairs a computer that has a working key but no phone with `npx pushary@latest pair`, run in the background.
  - Setup's `--agents` list names every agent id setup accepts, and an agent the Mac app connected is connected in the Mac app.
  - `sent: 0` or a `suppressed` reason means no device got the push, so the agent does not say it notified you. A reminder body over 180 characters fails the call.
  - An approval the run cannot continue without is still asked after three pushes, wherever you are.
  - A Partner customer can answer in the Mac notch once they connect the Mac from their phone.
- `pushary-human-in-the-loop` skill 0.1.2, from 0.1.0: MCP calls wait at most 48 seconds and REST waits 55; an agent runs setup in the background and keeps it running while the phone pairs.
- `pushary-chatgpt` 0.3.2, `pushary-cowork` 0.7.1, `hermes` 0.9.1 and `SKILL-LITE.md` 0.7.1: When away (`push_first`) still sends the agent's questions to the phone and lets only hook approvals skip it while you are at the computer, and a Partner customer can answer in the Mac notch once they connect the Mac from their phone. The ChatGPT skill also gives the 48 second wait, and the Hermes skill keeps notification bodies under the 180 characters the phone shows.
