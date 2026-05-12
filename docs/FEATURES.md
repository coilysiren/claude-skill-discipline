# Features

Baseline inventory of what `claude-skill-discipline` ships today. Use this as the reference point for scope changes. When a feature is added, removed, or materially reshaped, update the relevant section so the diff against this file shows scope drift over time.

Last full sweep: 2026-05-11.

## Pre-commit hooks

Exposed via [`.pre-commit-hooks.yaml`](../.pre-commit-hooks.yaml). Consumer repos pin the rev and add the hook IDs they want.

- **`skill-conventions`** ([validate_skills.py](../src/claude_skill_discipline/validate_skills.py)) - validates `.claude/skills/` against a repo-supplied `.claude/skills/categories.yaml` spec. Checks frontmatter, prefix taxonomy, status lines, required sections, size caps, stale skill-name references. Runs on `pre-commit` and `pre-push`.
- **`dead-cross-links`** ([check_dead_links.py](../src/claude_skill_discipline/check_dead_links.py)) - walks markdown inside `.claude/skills/` and fails on any inline `[text](path.md)` link that does not resolve. Runs on `pre-commit` and `pre-push`.
- **`commit-closes-issue`** ([check_commit_closes_issue.py](../src/claude_skill_discipline/check_commit_closes_issue.py)) - rejects commit messages without a GitHub closing keyword (`closes #N`, `fixes #N`, `resolves #N`) pointing at an issue in the same repo. Runs on `commit-msg`.

## Categories spec

- **Per-repo allowlist** at `.claude/skills/categories.yaml`. Each entry declares either a prefix family (`coding-*`, `playbook-*`) or an exact skill name (a router, a meta).
- **Rejection-by-default**: anything in `.claude/skills/` that does not match a category entry fails the hook.
- **Heavily commented example** at [examples/categories.yaml](../examples/categories.yaml).

## Consumer docs

- [docs/handbook.md](handbook.md) - the discipline these hooks enforce, with the why behind each rule.
- [docs/authoring-walkthrough.md](authoring-walkthrough.md) - how to draft, validate, and ship a new skill.
- [examples/pre-commit-config.yaml](../examples/pre-commit-config.yaml) - drop-in block for consumer `.pre-commit-config.yaml`.
- [templates/SKILL.md.template](../templates/SKILL.md.template) - minimal starter for a new skill.

## Packaging

- **Python package** `claude_skill_discipline` published with three console scripts (`validate-skills`, `check-dead-links`, `check-commit-closes-issue`) consumed by the pre-commit entries.
- **Versioned releases** via git tags. Consumers pin `rev:` in their pre-commit config.

## See also

- [README.md](../README.md) - human-facing intro and quickstart.

Cross-reference convention from [coilysiren/coilyco-ai#313](https://github.com/coilysiren/coilyco-ai/issues/313). The worked example is [otel-a2a-relay/docs/FEATURES.md](https://github.com/coilysiren/otel-a2a-relay/blob/main/docs/FEATURES.md).

## See also

- [README.md](../README.md) - human-facing intro.
- [AGENTS.md](../AGENTS.md) - agent-facing operating rules.
- [.coily/coily.yaml](../.coily/coily.yaml) - allowlisted commands.

Cross-reference convention from [coilysiren/coilyco-ai#313](https://github.com/coilysiren/coilyco-ai/issues/313).
