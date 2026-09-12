<p align="center">
  <h1 align="center">Pushary Agent Skill</h1>
  <p align="center">
    Push notifications and human-in-the-loop for AI agents. Approve from your phone.
    <br />
    <a href="https://pushary.com/ai-coding"><strong>Get started</strong></a>
    &nbsp;&middot;&nbsp;
    <a href="https://skills.sh/">Skills directory</a>
    &nbsp;&middot;&nbsp;
    <a href="https://github.com/pushary/pushary-skill/issues">Report a bug</a>
  </p>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT License" /></a>
  <a href="https://skills.sh/"><img src="https://img.shields.io/badge/skills.sh-listed-brightgreen" alt="skills.sh" /></a>
  <a href="https://www.npmjs.com/package/@pushary/agent-hooks"><img src="https://img.shields.io/npm/v/@pushary/agent-hooks" alt="npm" /></a>
  <a href="https://pypi.org/project/hermes-plugin-pushary/"><img src="https://img.shields.io/pypi/v/hermes-plugin-pushary" alt="PyPI" /></a>
  <a href="https://smithery.ai/servers/aadil/pushary"><img src="https://smithery.ai/badge/aadil/pushary" alt="Smithery" /></a>
  <a href="https://glama.ai/mcp/servers/Pushary/pushary-skill"><img src="https://glama.ai/mcp/servers/Pushary/pushary-skill/badges/score.svg" alt="Glama score" /></a>
</p>

## Start here

**[Try an approval without signing up](https://pushary.com/try?utm_source=github&utm_medium=oss-skill&utm_campaign=pushary-skill&utm_content=demo)**
→ connect an open phone page → approve or decline → see the agent continue or stop.
This browser demo uses polling and temporary state, not real push delivery or the production ledger.

| What you want to build | Start with |
| --- | --- |
| Notifications and questions for a coding agent | The setup instructions below |
| An enforced tool approval in the Vercel AI SDK | [Runnable AI SDK example](https://github.com/Pushary/pushary-ai-sdk/tree/main/examples) |
| A LangGraph workflow that branches on a human decision | [Runnable LangGraph example](https://github.com/Pushary/pushary-langgraph/tree/main/examples) |
| Another agent framework | [All Pushary integrations](https://github.com/Pushary) |

The skill and adapters are MIT-licensed. Hosted phone delivery requires a Pushary
account; embedding approvals for your own users requires Partner access.
The two framework examples also run locally with simulated answers and no credentials.

If Pushary helped you, [star this repo](https://github.com/Pushary/pushary-skill).
Want to help? [Public issues and patches are welcome](CONTRIBUTING.md).

---

Your AI agent finishes a 20-minute refactor while you're making coffee. Without Pushary, you'd never know until you checked back. With Pushary, you get a push notification on your phone the moment it's done - or a question on your lock screen when the agent needs a decision. It's the tool behind "keep going, ping me on my phone if you need anything".

Pushary is a hosted service: $9.99/mo after a 3-day card-first trial. It works with Claude Code, Claude Cowork, Codex, Cursor, Windsurf, Hermes, Lovable, or any MCP client, and it does not need a Claude Max subscription. If you run Claude Code with Claude Max, Anthropic Remote Control covers that one setup for free. Pushary covers what it does not: Codex, Cursor, Claude Code without Max, a cross-agent fleet, enforced policy gating, lock screen answer buttons, and an audit trail of every question and answer.

## How It Works

```
AI Agent  ->  MCP Protocol  ->  Pushary API  ->  Push Notification  ->  Your Phone
```

Pushary is an [MCP server](https://modelcontextprotocol.io/) that connects your AI coding agent to push notifications. The agent calls Pushary's tools over the Model Context Protocol, and you receive notifications instantly on any device.

**Three question types** - yes/no confirmations, multiple choice, and free text input. Your agent picks the right one for the situation.

**Rich context notifications** - agents can include file changes, error details, and suggested next steps in a detail page you see when tapping the notification.

**Agent identification** - when you run multiple agents, each notification shows which agent is asking so you always know what you're responding to.

**Permission hooks** - route Claude Code's tool approval prompts through push notifications so you can approve or deny from your phone.

---

## Personal setup: phone first

Run the pairing setup in your agent's terminal:

```bash
npx @pushary/agent-hooks@latest setup
```

Install [Pushary on your phone](https://pushary.com/download), scan the QR or open the printed link, compare the fingerprints, and approve. Setup then configures the selected agent's tools, supported hooks and skill. You do not need to copy an API key first. Personal accounts require a plan; the current offer is $9.99/mo after a 3-day card-first trial. Existing credentials are reused.

Verify setup:

```bash
npx @pushary/agent-hooks@latest doctor
```

Ask your agent for one harmless test question. Confirm the intended device receives it and the answer returns to the same task.

For Hermes specifically:

```bash
npx @pushary/agent-hooks@latest setup --agents hermes
```

This installs the native plugin in Hermes' interpreter and configures it. The plugin provides notification, question, wait, cancel and scope tools, plus Partner enrollment and customer-question tools. Its parameters use snake_case; use the [Hermes skill](skills/hermes/SKILL.md), not MCP parameter names. The native approval transport preserves Hermes' session and standing approvals.

## Mac notch and other answer surfaces

Install [Pushary for Mac](https://pushary.com/download), sign in with the same personal account and connect your agents from the app. The notch provides confirm, choice, text and question-set controls with keyboard support. Keep the app running for presence and delivery; verify one harmless question there, then phone fallback while away from the Mac.

| Surface | Answers |
| --- | --- |
| Mobile | Confirm, choice and text in the app. Supported confirm notifications expose lock-screen approve/deny; arbitrary choices and text open the app. |
| Mac notch | Personal account questions, including keyboard answering. No Partner customer inbox is currently provided. |
| Slack | Buttons, choice menus and text modals when the integration and recipient are configured. |
| Browser | Decision-page fallback and browser notifications when permission is enabled. |

Delivery follows your settings and presence. `push_first` uses presence, `push_only` requests push every time, `notify_only` keeps decisions in the current client, and `terminal_only` avoids push. A successful API call alone does not prove a device displayed the request.

## Partner setup: your own customers

Personal pairing connects you, the operator. Partner integrations enroll each of your application's customers through a scoped connection link and address decisions using your stable customer ID. Customers use the mobile enrollment flow; do not route their approvals to your personal Mac inbox or share an operator API key.

Start with the [Partner integration guide](https://pushary.com/docs/agents/embed), then a [Mastra agent example](https://github.com/Pushary/pushary-mastra/tree/main/examples), [AI SDK example](https://github.com/Pushary/pushary-ai-sdk/tree/main/examples) or [LangGraph example](https://github.com/Pushary/pushary-langgraph/tree/main/examples). Tools choose confirm, choice or text. Approving an action and supplying an answer are different operations; a text answer saying “yes” is not permission to execute another tool.

## Browser and manual fallbacks

For browser pairing:

```bash
npx @pushary/agent-hooks@latest setup --connect browser
```

For a client that needs manual MCP configuration, obtain your key in the Pushary dashboard and configure:

```json
{
  "mcpServers": {
    "pushary": {
      "url": "https://pushary.com/api/mcp/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

Keep the key private. Install the skill with `npx skills add Pushary/pushary-skill` if the client supports skills.sh. Manual MCP provides cooperative questions and notifications; enforced approvals require the supported host hooks/runtime integration.

Claude Code users can alternatively install this repository as a plugin:

```
/plugin marketplace add Pushary/pushary-skill
/plugin install pushary
```

That path reads `PUSHARY_API_KEY` from the environment. Pick one installation path: installing the plugin on top of CLI-installed Claude hooks can run hooks twice. Use `npx @pushary/agent-hooks@latest clean` before switching paths.

---

## Setup: Lovable

[Lovable](https://lovable.dev) is a hosted app builder, so there is no local install step (`npx skills add` does not apply). You connect the MCP server in Lovable's UI, then paste the skill into Lovable's Knowledge so the agent uses it on its own.

**1. Connect the MCP server.** In Lovable, go to **Settings -> Connectors -> Personal connectors** (paid Lovable plans), click **New MCP server**, set the URL to `https://pushary.com/api/mcp/mcp`, choose **Bearer token**, and paste your API key (`pk_xxx.sk_xxx`).

**2. Add the skill to Knowledge.** Lovable cannot install skills via `npx`, so paste the skill guidance into **Settings -> Knowledge**. Use the condensed [`SKILL-LITE.md`](skills/pushary/SKILL-LITE.md) as the source. This makes the Lovable agent notify you when a build finishes and ask before risky changes, without you prompting each time.

Lovable gets notifications and questions only (no enforced gate, since it has no permission hook). Full walkthrough: [Lovable guide](https://pushary.com/docs/agents/guides/lovable).

---

## Setup: Claude Cowork

[Claude Cowork](https://claude.com/) is Anthropic's agentic workspace inside the Claude apps. It is a hosted surface (`npx skills add` does not apply), so you connect Pushary as a custom connector and add the skill through Cowork's own skill upload.

**1. Connect the MCP server.** Get your connector link from the [Pushary dashboard](https://pushary.com/dashboard/agent/settings) (**Settings -> Connections**, Claude section). In Claude, open **Settings -> Connectors -> Add custom connector**, leave the OAuth fields empty, and paste the link. Connectors are account level, so the same connector is available inside Cowork; enable it in a session under **Customize -> Connectors**.

**2. Add the skill.** Zip the [`skills/pushary-cowork`](skills/pushary-cowork) folder and upload it in Cowork under **Customize -> Skills** (skills need code execution enabled). Alternatively, paste the standing instructions block from your Pushary dashboard into Claude **Settings -> Cowork**, so sessions ask for unresolved decisions and report meaningful unattended results while respecting authorization already given.

Cowork gets notifications and questions only (no enforced gate; Cowork exposes no hooks). Full walkthrough: [Claude Cowork guide](https://pushary.com/docs/agents/guides/claude-desktop). Dedicated plugin repo: [Pushary/cowork-plugin](https://github.com/Pushary/cowork-plugin).

---

## Tools

The skill guides these MCP tools:

| Tool | Description |
|------|-------------|
| `send_notification` | Send a push notification with optional rich context (file changes, errors, next steps) |
| `ask_user` | Ask the user a question via push - yes/no, multiple choice, or free text |
| `wait_for_answer` | Long-poll for the user's response to a question |
| `cancel_question` | Cancel a pending question that's no longer relevant |
| `list_sessions` | Read-only view of your live agent sessions and pending questions |
| `propose_scope` | Ratify an unresolved or requested file boundary on supported hosts |

Full tool documentation with parameters, examples, and usage guidelines is in [`skills/pushary/SKILL.md`](skills/pushary/SKILL.md).

## Human-in-the-Loop

The agent can ask you decisions via push and wait for your answer:

```
Agent: "Which auth strategy should I use?"
  Options: JWT tokens / Session cookies / OAuth2 + PKCE
  -> push to your phone

You: tap "JWT tokens"

Agent: proceeds with JWT implementation
```

Supports three question types:
- **Confirm** - yes/no binary decisions
- **Select** - pick from 2-6 options
- **Input** - free text response

Read `answered`, `status` and `handoffAction` (or `nextAction`) from the tool result. Poll only as directed; cancel a live question before handing it to another surface and honor any winning answer. Expiry and waits follow server policy. Silence is not approval, and a late reply cannot restart an ended agent turn.

## Packages

| Package | Registry | Description |
|---------|----------|-------------|
| [`@pushary/agent-hooks`](https://www.npmjs.com/package/@pushary/agent-hooks) | npm | Claude Code permission hooks |
| [`hermes-plugin-pushary`](https://pypi.org/project/hermes-plugin-pushary/) | PyPI | Hermes Agent native plugin |

## Compatible Agents

Works with any agent that supports [skills.sh](https://skills.sh/) or the Model Context Protocol:

- [Claude Code](https://code.claude.com/) (MCP + permission hooks)
- [Claude Cowork](https://claude.com/) (custom connector + skill upload)
- [Hermes Agent](https://hermes-agent.nousresearch.com/) (native plugin or MCP)
- [Cursor](https://cursor.com/) (MCP)
- [Windsurf](https://windsurf.com/) (MCP)
- [Lovable](https://lovable.dev/) (MCP connector + skill via Knowledge)
- [OpenAI Codex](https://openai.com/index/openai-codex/) (MCP)
- And [39+ more agents](https://skills.sh/)

## Publishing the MCP registry entry

`server.json` is not published by merging it. The [MCP registry](https://registry.modelcontextprotocol.io) serves one record per version, so an edit here reaches nobody until the new version is pushed to the registry, and the old text keeps being what every agent reads.

Everyday path: bump `version` in `server.json`, merge to `main`, then sync the public skill mirror. Its release workflow publishes the new registry entry.

The release workflow publishes from the public Pushary repository so GitHub OIDC can prove ownership of the `io.github.Pushary/pushary` namespace. Verify the workflow and registry result after release.

Manual fallback, from this directory:

```
curl -L "https://github.com/modelcontextprotocol/registry/releases/latest/download/mcp-publisher_$(uname -s | tr '[:upper:]' '[:lower:]')_$(uname -m | sed 's/x86_64/amd64/;s/aarch64/arm64/').tar.gz" | tar xz mcp-publisher
./mcp-publisher login github
./mcp-publisher publish
```

Check what the registry actually serves, which is the only number that matters:

```
curl -s "https://registry.modelcontextprotocol.io/v0/servers?search=pushary"
```

That endpoint returns **every** version, oldest first. Read the entry whose `_meta."io.modelcontextprotocol.registry/official".isLatest` is `true`; the first row in the list is the oldest record, not the live one.

## Contributing

Contributions are welcome! Please read the [contributing guide](CONTRIBUTING.md) before submitting a pull request.

## Security

If you discover a security vulnerability, please report it responsibly. See [SECURITY.md](SECURITY.md) for details.

## Funding

This project is fully funded by **[RalphNex OU](https://ralphnex.com/)**, an Estonian software development agency.

## License

[MIT](LICENSE) - Copyright (c) 2025 RalphNex OU
