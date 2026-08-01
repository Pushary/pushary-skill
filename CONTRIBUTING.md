# Contributing to Pushary Agent Skill

Thanks for your interest in contributing! This guide will help you get started.

## How to Contribute

### Reporting Bugs

If something isn't working as expected, [open a bug report](https://github.com/pushary/pushary-skill/issues/new?template=bug_report.md). Please include:

- Which AI agent you're using (Cursor, Claude Code, Windsurf, etc.)
- Your MCP configuration
- What you expected to happen vs. what actually happened
- Steps to reproduce the issue

### Suggesting Features

Have an idea for improving the skill? [Open a feature request](https://github.com/pushary/pushary-skill/issues/new?template=feature_request.md). We'd love to hear how Pushary could work better for your workflow.

### Submitting Changes

**This repository is a published copy, not where the skill is written.** The
source lives in Pushary's private monorepo and is pushed here whenever it
changes, so a commit landed directly on this repo is overwritten by the next
publish. Two pull requests were merged here and then sat stranded for a month
for exactly that reason.

So please **open an issue** rather than a pull request. Paste your diff into it
if you already have one, and it will be applied upstream and credited to you.
A pull request opened here is read and treated the same way, as a patch to
apply upstream, but it will not be merged as a pull request, because merging it
would guarantee it gets erased.

Security issues: email aadil@pushary.com rather than opening an issue.

### What Can I Contribute?

This repository contains the skill definition (not the Pushary backend), so contributions typically involve:

- **Documentation** — Improving setup guides, examples, or tool descriptions
- **Skill definition** — Better usage guidelines, new notification patterns, improved etiquette rules
- **Templates** — Better issue/PR templates
- **Integrations** — Examples for specific agents or workflows

### Style Guidelines

- Keep documentation clear and concise
- Use concrete examples over abstract descriptions
- Test any configuration changes with at least one MCP-compatible agent

## Questions?

If you're not sure about something, [open a discussion](https://github.com/pushary/pushary-skill/issues) or reach out. We're happy to help.
