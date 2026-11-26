# Glassray plugin

Set up tracing, create and calibrate custom evaluators, and test fixes from Claude Code or Codex. The plugin connects your agent to Glassray over MCP.

## Installation

### Claude Code

```bash
claude plugin marketplace add glassray/agent-plugin
claude plugin install glassray@glassray-skills
```

Start a new Claude Code session. Run `/mcp`, select `plugin:glassray:glassray`, and sign in to Glassray in your browser.

### Codex

```bash
codex plugin marketplace add glassray/agent-plugin
codex plugin add glassray@glassray-skills
```

Start a new Codex session and sign in to Glassray when prompted. In the desktop app, you can also install **glassray** from **Glassray** in the Plugins directory after adding the marketplace.

## Update

The marketplace ID is still `glassray-skills`, so existing installs use the same update commands.

- **Claude Code** — run `claude plugin marketplace update glassray-skills`, then `claude plugin update glassray@glassray-skills`.
- **Codex** — run `codex plugin marketplace upgrade glassray-skills`, then `codex plugin add glassray@glassray-skills`.

## Try it

- “Set up Glassray tracing for this repo.” The agent checks the tracing you already have and verifies a test trace in Glassray.
- “Draft a Glassray evaluator for DEV-123.” Use a deviation number from your Glassray workspace.
- “Resume calibration for DEV-123/E1.” Use the reference for your evaluator.
- “Test my fix on this branch for DEV-123.” The agent replays stored traces at production's commit and at your branch, judges both and records a fix test.

Local evaluator checks and replays require Node.js and `npx`; replays also need `git`. See the [Glassray MCP setup guide](https://glassray.ai/docs/mcp-server) to connect an API key or another client.
