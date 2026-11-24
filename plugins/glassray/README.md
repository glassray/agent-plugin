# Glassray evaluator plugin

Create and calibrate custom evaluators from Claude Code or Codex. The plugin includes two skills and connects your agent to Glassray over MCP.

## Installation

### Claude Code

```bash
claude plugin marketplace add glassray/skills
claude plugin install glassray@glassray-skills
```

Start a new Claude Code session. Run `/mcp`, select `plugin:glassray:glassray`, and sign in to Glassray in your browser.

### Codex

```bash
codex plugin marketplace add glassray/skills
codex plugin add glassray@glassray-skills
```

Start a new Codex session and sign in to Glassray when prompted. In the desktop app, you can also install **glassray** from **Glassray** in the Plugins directory after adding the marketplace.

## Try it

- “Draft a Glassray evaluator for DEV-123.” Use a deviation number from your Glassray workspace.
- “Resume calibration for DEV-123/E1.” Use the reference for your evaluator.

Local evaluator checks require Node.js and `npx`. See the [Glassray MCP setup guide](https://glassray.ai/docs/mcp-server) to connect an API key or another client.
