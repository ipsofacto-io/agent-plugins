---
name: decision-health
description: Check whether an engineering decision (for example on firewalls, TLS, or storage) is still valid in force and respected across CI changes and live infrastructure state. Use whenever the user asks if a decision still holds, is respected, has drifted, or is stale.
---

# Check decision validity and compliance health

1. Call `decisions.health` with `id` when you know the decision's slug or identifier, or `query` with topic keywords (for example `"firewall"`), optionally narrowed by `repo` and `env`.
2. Answer with two distinct dimensions:
   - **Validity**: state (`valid`, `needs_review`, `superseded`, `expired`), `valid_from`, `valid_to`, `review_by`, `superseded_by`, and any `open_premise_reasons`.
   - **Compliance**: aggregate state and per-scope breakdown (`compliant`, `violated`, `waived`, `stale`, `unverified`), showing both `latest_change` (CI pull request or push) and `latest_live` (scheduled OIDC or signed `ipso-agent` drift check), whether evidence is `stale` relative to `max_age_days`, active waivers or baselines, and `AI-generated` (`model_id`, `prompt_version`) when an `engine: llm` check produced the verdict.
3. If `decisions.health` returns `stale` or `unverified` for live infrastructure, tell the user that no recent live snapshot verdict is on record within `max_age_days` rather than claiming the infrastructure is compliant.
