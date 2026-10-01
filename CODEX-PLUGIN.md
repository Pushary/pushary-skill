# The ChatGPT and Codex plugin

This package is two distributions of one skill set. `.claude-plugin/plugin.json` is the
Claude Code plugin. `.codex-plugin/plugin.json` is the ChatGPT and Codex plugin. They share
`skills/`, `logo.png` and `.mcp.json`; nothing is duplicated.

Published plugins land in **one directory shared by ChatGPT and Codex**, so this is a single
submission that reaches both.

## Testing it locally, before OpenAI is involved

The repo root carries `.agents/plugins/marketplace.json`, a local marketplace pointing at
this directory. That makes the plugin installable and runnable now, with no plugin id, no
submission and no review:

```bash
codex plugin marketplace add .           # from the repo root
codex plugin marketplace list            # expect "pushary-local"
```

Then install `pushary` from the Plugins directory and run the evaluation set: a direct ask
("notify me when this finishes"), an indirect one ("I'm stepping away"), an incomplete one,
and a case that should *not* activate it. This is the loop to iterate the skills in.

## What is deliberately not here

**Hooks.** Codex explicitly declares `hooks: []` to disable discovery of
`hooks/hooks.json`, which is the Claude Code plugin's generated configuration.
Native Codex hooks are installed by `pushary setup`. Omitting the field would
cause Codex to discover the Claude configuration after hook trust is granted.
See [OpenAI's bundled hook rules](https://developers.openai.com/plugins/build/plugins).

**`.app.json`.** For a hosted plugin, OpenAI wants the MCP server registered through the
portal and referenced by id, not the keyed HTTP config in `.mcp.json`. That id does not
exist yet. `.mcp.json` is still correct for a Codex user running locally with
`PUSHARY_API_KEY` set, so it stays; `.app.json` is added alongside it once we hold an id.

**Screenshots.** `interface.screenshots` is omitted rather than pointed at files that are
not in this package. The submission form collects them separately.

## Published ChatGPT listing

Pushary is approved and listed at
[Pushary in ChatGPT](https://chatgpt.com/plugins/plugin_asdk_app_6abc2ebca100819198c8619e9a697ee3).
Mobile onboarding, web onboarding and settings use `CHATGPT_PLUGIN_URL` from
`@pushary/contracts`; no plugin ID environment variable is required.

## Keeping the two manifests honest

`version` is tracked per distribution and they will drift; that is fine, they are separate
artifacts. What must not drift is the description of what the skill does, because OpenAI
review checks description accuracy against behaviour. The skill bodies themselves are
generated from one source and gated in CI by `node scripts/sync-skill.mjs --check`.
