# agent-plugins

[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/ipsofacto-io/agent-plugins/badge)](https://scorecard.dev/viewer/?uri=github.com/ipsofacto-io/agent-plugins)

Plugins and hook configurations that keep AI coding agents (Claude Code, Codex, Copilot, Cursor, and Kiro) aligned with your repository's [ipso](https://github.com/ipsofacto-io/ipso) engineering decisions.

> Prerequisites: install the `ipso` CLI on your `PATH` and run `ipso init` in your repository. Base hooks and MCP require `ipso` v0.4.0+; `decisions.health`, `decisions.verifiability`, and the `decision-health` skill require `ipso` v0.5.0+.

## Claude Code

Add this repository as a marketplace and install the plugin:

```bash
claude plugin marketplace add ipsofacto-io/agent-plugins
claude plugin install ipso@ipso
```

### What the plugin installs

| Component | What it does |
| --- | --- |
| `SessionStart` hook | Compiles decisions locally into `.ipso/build` (git-ignored) and loads the decisions currently in force |
| `PreToolUse` hook | Checks proposed edits (`Edit`, `Write`, `MultiEdit`, `NotebookEdit`, `Bash`) against `agent-edit` rules; denies `block` violations with a link to the decision and asks a person on `warn` violations |
| `Stop` hook | Re-checks the working tree before finishing and feeds blocking violations back to the agent once |
| `propose-decision` skill | Checks verifiability via `decisions.verifiability` and creates a proposed EDR via `decisions.propose` for human approval |
| `decision-health` skill | Queries `decisions.health` to report separate Validity and Compliance (CI `change` vs `live` drift, plus `stale` freshness) |
| Local MCP server | Exposes `ipso mcp` with `decisions.applicable`, `decisions.get`, `decisions.health`, `decisions.verifiability`, `checks.evaluate`, and `decisions.propose` |

### Using a signed policy bundle

By default, the plugin evaluates your local `.ipso/build` output. To evaluate a signed OCI policy bundle instead, set `bundle_dir`, `bundle_digest`, and `bundle_key` when enabling the plugin.

## Codex, Copilot, Cursor, and Kiro

Each subdirectory under `agents/` contains that agent's hook file at its expected repository path. Copy the file into your repository and make sure `ipso` is installed on `PATH`.

| Agent | Hook file to copy | Extra setup |
| --- | --- | --- |
| Codex | [`agents/codex/.codex/hooks.json`](agents/codex/.codex/hooks.json) | Trust the project and review hooks with `/hooks` |
| Copilot | [`agents/copilot/.github/hooks/ipso.json`](agents/copilot/.github/hooks/ipso.json) | For Copilot cloud agent, install `ipso` in setup steps or every tool call stops |
| Cursor | [`agents/cursor/.cursor/hooks.json`](agents/cursor/.cursor/hooks.json) | None |
| Kiro | [`agents/kiro/.kiro/hooks/ipso.json`](agents/kiro/.kiro/hooks/ipso.json) | None |

For MCP server setup across agents, see the [ipso MCP guide](https://github.com/ipsofacto-io/ipso/blob/main/docs/mcp.md).

## How enforcement works

- **100% local and deterministic:** Hooks evaluate compiled rules on your machine with no LLM calls and no outbound network traffic.
- **Non-intrusive on clean edits:** Hooks return no decision when an edit passes, so your own permission rules remain in effect.
- **Repository-scoped:** Hooks only activate in repositories that contain an `.ipso` directory (created by `ipso init`).
- **Guardrail, not a sandbox:** Each agent enforces hooks differently (and shell commands can obscure touched files). In Claude Code, a missing `ipso` binary fails open with a notice. Always keep CI checks as your required merge gate.

Live enforcement was verified with `ipso` v0.4.0 in Claude Code, Codex, Copilot CLI, Cursor, and Kiro (the Copilot cloud agent has not been live-tested yet). See [ipso hooks documentation](https://github.com/ipsofacto-io/ipso/blob/main/docs/hooks.md) for the exact enforcement matrix per agent.

## License

Apache License, Version 2.0. See [LICENSE](LICENSE).

Copyright 2026 Luca Bertelli.

## Contributing

Follow the contributor guidelines in [ipsofacto-io/.github](https://github.com/ipsofacto-io/.github/blob/main/CONTRIBUTING.md). All commits require a `Signed-off-by` trailer (`git commit -s`).
