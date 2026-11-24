---
name: decision-health
description: Check whether an engineering decision (for example on firewalls, TLS, or storage) is still valid in force and respected across CI changes and live infrastructure state. Use whenever the user asks if a decision still holds, is respected, has drifted, or is stale.
---

# Check decision validity and compliance health

1. Always pass at least one of `ref` (when you know the decision's slug or identifier) or `query` (topic keywords extracted from the user's question, for example `"firewall"`), optionally narrowed by `repo` and `env`. Do not call `decisions.health` with both `ref` and `query` empty.
2. Answer with two distinct dimensions:
   - **Validity**: first check whether `compliance.note` indicates offline local `ipso mcp` evaluation without a control plane; when offline, `superseded_by` reflects only local `supersedes` links in the checkout and `open_premise_reasons` are not probed locally, so state that limitation up front and recommend `ipso status`, `ipso premise check`, or remote MCP when live premise or registry supersession state matters. Report state (`valid`, `needs_review`, `superseded`, `expired`, `deprecated`), `valid_from`, `valid_to`, `review_by`, `superseded_by`, and any `open_premise_reasons`.
   - **Compliance**: first check whether `compliance.note` indicates local checkout evaluation without a control plane (`compliance.state` is `unverified` unless an active checkout waiver applies, and `latest_change` / `latest_live` are absent). When running against offline local `ipso mcp`, state up front that stored CI and live drift verdicts are unavailable locally and recommend `ipso status` or remote MCP rather than inventing or overstating compliance. When server verdicts are present, report aggregate state and per-scope breakdown (`compliant`, `violated`, `waived`, `stale`, `unverified`), showing both `latest_change` (CI pull request or push) and `latest_live` (scheduled OIDC or signed `ipso-agent` drift check), whether evidence is `stale` relative to `max_age_days`, active waivers or baselines, and `AI-generated` (`model_id`, `prompt_version`) when an `engine: llm` check produced the verdict.
3. Whenever `compliance.state` is `stale` or `unverified`, tell the user clearly that no recent verified CI or live snapshot verdict within `max_age_days` proves compliance.
