# agent-plugins

[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/ipsofacto-io/agent-plugins/badge)](https://scorecard.dev/viewer/?uri=github.com/ipsofacto-io/agent-plugins)

Claude Code, Cursor, Copilot and Kiro plugins that keep AI coding agents within ipso decisions.

> Status: the Claude Code plugin needs an `ipso` release that has `ipso hook` and `ipso mcp`. Until that release, build the CLI from `main`. It has been checked with `claude plugin validate` and its hook command has been tested against the documented event format. It has not run in a live Claude Code session.

Agent marketplaces install from this repository. It holds the Claude Code marketplace and the install surfaces for the other agents. Plugins call a released CLI and the local MCP server.

## Claude Code

Install `ipso` so it is on `PATH`, then:

```bash
claude plugin marketplace add ipsofacto-io/agent-plugins
```

```bash
claude plugin install ipso@ipso
```

| Part | What it does |
| --- | --- |
| `SessionStart` hook | Runs `ipso compile` for the local build, which git ignores, and states the decisions in force |
| `PreToolUse` hook on `Edit`, `Write`, `MultiEdit`, `NotebookEdit`, and `Bash` | Evaluates `agent-edit` checks on the proposed content. A violated `block` check denies with a link to the decision. A violated `warn` check asks a person |
| `Stop` hook | Checks the working tree and gives blocking violations back to the agent once |
| `propose-decision` skill | Creates a proposal through `decisions.propose`. A person approves it |
| MCP server | `ipso mcp`, with `decisions.applicable`, `decisions.get`, `checks.evaluate`, and `decisions.propose` |

The hook decides on this machine with no LLM and sends nothing anywhere. It gives no answer on a clean edit, so your own permission rules still apply. It only acts in a repository with an `.ipso` directory, so run `ipso init` first.

To use a signed policy bundle instead of the local build, set `bundle_dir`, `bundle_digest`, and `bundle_key` when you enable the plugin.

A missing `ipso` binary makes the hook fail open with a notice, because Claude Code treats that as a non-blocking error. CI remains the gate. The hook is a guard for a cooperating agent and not a sandbox, so a shell command can hide what it touches.

## License

Apache License, Version 2.0. See [LICENSE](LICENSE).

Copyright 2026 Luca Bertelli.

## Contributing

Follow the contributor terms in [ipsofacto-io/.github](https://github.com/ipsofacto-io/.github/blob/main/CONTRIBUTING.md). Commits need a `Signed-off-by` trailer (`git commit -s`).
