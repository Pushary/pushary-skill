# Installing Pushary

Pushary is a hosted remote MCP server. There is nothing to clone, build, or run locally.

## If you can run shell commands, do this instead

One command, and it needs no account and no API key up front:

```bash
npx -y pushary@latest setup
```

On Windows, run `npx.cmd -y pushary@latest setup`.

It prints a short link, a QR code and a fingerprint, then waits up to 15
minutes for the phone. That is longer than most clients let a foreground
command run, and a killed setup kills the pairing code with it. So run it in
the background with its output going to a log, and keep it running. Read the
link, the QR and the fingerprint from the log and show them to the user as
they print. They open the link or scan the QR with their phone's camera or the
Pushary app, sign in, check the fingerprint and approve. That pairs their
Pushary app, which is what hands this machine its key. Keep checking the log
until setup exits: only then has it written the MCP config, hooks and
permissions. Everything below is only for clients that cannot run a command.

To pair a phone later on a machine that already ran setup, run
`npx -y pushary@latest pair` the same way: in the background, kept running until
the phone approves, with its link, QR and fingerprint shown to the user. It
keeps the machine's key.

## What you need for a manual install

A Pushary API key in the format `pk_xxx.xxx`. Get one at https://pushary.com/sign-up
(the agent plan is $9.99/mo after a 3 day trial). The key is the only required value.

## Steps

1. Ask the user for their Pushary API key. Do not invent one and do not continue without it.

2. Add the server to the MCP settings file for this client. For Cline that is
   `cline_mcp_settings.json`; for Claude Code it is `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "pushary": {
      "url": "https://pushary.com/api/mcp/mcp",
      "headers": {
        "Authorization": "Bearer pk_xxx.xxx"
      }
    }
  }
}
```

Replace `pk_xxx.xxx` with the user's key.

3. Reload MCP servers. `tools/list` should return `send_notification`, `ask_user`,
   `wait_for_answer` and `cancel_question` among others.

## Verifying the install

Call `send_notification` with a short title and body. The user should get a push within a
few seconds. If nothing arrives, they have not paired a device yet: send them to
https://pushary.com/dashboard/agent/settings to connect their phone or browser.

## Notes for clients that cannot send headers

The key can ride in the URL instead:

```
https://pushary.com/api/mcp/mcp?api_key=pk_xxx.xxx
```

An SSE transport is available at `https://pushary.com/api/mcp/sse`.

`initialize` and `tools/list` answer without a key, so introspection works before setup.
Every tool call needs the key.

## When to use the tools

- `ask_user` when you need a decision from the user, then `wait_for_answer`. Types are
  `confirm` for yes/no, `select` for a fixed set of options, `input` for free text.
- `send_notification` when a long task finishes or you hit an error you cannot resolve.
- `cancel_question` to retract a pending question that is no longer needed.

Pass `agentName` so the user knows which session is asking.
