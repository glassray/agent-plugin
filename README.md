# Glassray agent plugin

Set up tracing and create and calibrate custom evaluators with [Glassray](https://glassray.ai).
The plugin bundles three [Agent Skills](https://agentskills.io) and connects your coding agent to your Glassray workspace over MCP.

## Skills included

- **Set up tracing.** Connect the tracing in your repo and verify that a test trace reaches Glassray. [Tracing skill](plugins/glassray/skills/glassray-otel/SKILL.md)
- **Create an evaluator.** Use evidence from traces to draft and test an evaluator for a deviation. [Creation skill](plugins/glassray/skills/glassray-create-evaluator/SKILL.md)
- **Calibrate an evaluator.** Test the draft on sampled traces, resolve edge cases, and check how consistently it behaves. [Calibration skill](plugins/glassray/skills/glassray-calibrate-evaluator/SKILL.md)

## Install the plugin

### Claude Code

```bash
claude plugin marketplace add glassray/agent-plugin
claude plugin install glassray@glassray-skills
```

Start a new session and use `/mcp` to sign in to Glassray.

### Codex

```bash
codex plugin marketplace add glassray/agent-plugin
codex plugin add glassray@glassray-skills
```

Start a new session and sign in to Glassray when prompted.

Try “Set up Glassray tracing for this repo.” If your traces are already flowing, try “Draft a Glassray evaluator for DEV-123,” using a deviation number from your workspace. See the [plugin guide](plugins/glassray/README.md) for how to update the plugin or resume calibration.

## Install tracing on its own

The standalone install includes the tracing setup skill. Use it to send traces or troubleshoot an OpenTelemetry exporter. [OpenTelemetry setup guide](https://glassray.ai/docs/otlp-ingestion)

Use the `skills` CLI with agents that support Agent Skills.

```bash
npx skills add glassray/agent-plugin --skill glassray-otel
```

In Claude Code, you can also install it from the marketplace.

```bash
claude plugin marketplace add glassray/agent-plugin
claude plugin install glassray-otel@glassray-skills
```

You can also give your agent the [tracing skill as a single file](https://glassray.ai/SKILL.md).

## Contributing

See [AGENTS.md](AGENTS.md) for the layout and the rules a skill here has to follow. Every push
runs the Agent Skills reference validator and checks every link in every skill.

MIT - see [LICENSE](LICENSE).
