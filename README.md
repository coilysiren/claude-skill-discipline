# claude-skill-discipline

> **Deprecated.** This repo has been merged into [`coilysiren/agentic-os`](https://github.com/coilysiren/agentic-os).
>
> - **Scripts**: `validate-skills.py` and `check-dead-links.py` now live in [`coilysiren/agentic-os/scripts/`](https://github.com/coilysiren/agentic-os/tree/main/scripts). `check-commit-closes-issue.py` was already there.
> - **Docs**: handbook + authoring walkthrough + examples + template moved to [`coilysiren/agentic-os/docs/skill-discipline/`](https://github.com/coilysiren/agentic-os/tree/main/docs/skill-discipline).
> - **Rollout**: consumer repos receive stamped local copies via [`coilysiren/agentic-os-kai`](https://github.com/coilysiren/agentic-os-kai) using `make apply-skill-discipline-hooks`. No more `repo: https://github.com/coilysiren/claude-skill-discipline` block in `.pre-commit-config.yaml`.
> - **Migration**: [agentic-os-kai#544](https://github.com/coilysiren/agentic-os-kai/issues/544).
>
> Pin at `v0.6.0` to keep this repo's hooks working in the meantime, but switch over when convenient. This repo will be archived once all consumers are migrated.

---

Pre-commit hooks for Claude Code skill repositories. Three small validators that catch the kinds of drift agent harnesses silently route around.

## What's in the box

Three hooks, exposed via [`.pre-commit-hooks.yaml`](.pre-commit-hooks.yaml):

* **`skill-conventions`** - validates the structure of `.claude/skills/` against a spec at `.claude/skills/categories.yaml`. Checks frontmatter, prefix taxonomy, status lines, required sections, size caps, stale skill-name references.
* **`dead-cross-links`** - walks markdown inside `.claude/skills/`, fails if any inline `[text](path.md)` link does not resolve.
* **`commit-closes-issue`** - rejects commits whose message lacks a `closes #N` / `fixes #N` / `resolves #N` keyword pointing at an issue in the same repo.

And the consumer-facing docs:

* [`docs/handbook.md`](docs/handbook.md) - the discipline these hooks enforce, with the why behind each rule.
* [`docs/authoring-walkthrough.md`](docs/authoring-walkthrough.md) - how to draft, validate, and ship a new skill.
* [`examples/categories.yaml`](examples/categories.yaml) - a heavily commented example spec to start from.
* [`examples/pre-commit-config.yaml`](examples/pre-commit-config.yaml) - the block to drop into your repo's pre-commit config.
* [`templates/SKILL.md.template`](templates/SKILL.md.template) - minimal starter for a new skill.

## Usage

In your consumer repo, add this block to `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/coilysiren/claude-skill-discipline
    rev: v0.6.0
    hooks:
      - id: skill-conventions
      - id: dead-cross-links
      - id: commit-closes-issue
```

Then create `.claude/skills/categories.yaml` (start from [`examples/categories.yaml`](examples/categories.yaml)). Each entry there declares either a prefix family (`coding-*`, `playbook-*`) or an exact-name skill (a router, a meta) that your repo allows. The validator rejects anything that does not match an entry, which is what keeps your skill surface coherent.

`pre-commit install` once, then every commit goes through the hooks. CI invokes `pre-commit run --all-files` the same way.

### Custom skills directory

If your repo keeps skills somewhere other than `.claude/skills/` (e.g. `.agents/skills/`), pass `--skills-dir` to each hook via `args:`:

```yaml
- id: skill-conventions
  args: [--skills-dir, .agents/skills]
- id: dead-cross-links
  args: [--skills-dir, .agents/skills]
```

`categories.yaml` is read from inside the resolved skills directory, so no separate config is needed for the spec location.

## Versioning

Tags follow semver. Breaking changes to the spec format or the validator rules mean a major bump. New optional checks, new spec fields, or bug fixes are minor or patch.

Pin to a tag in `rev:`. Re-run `pre-commit autoupdate` periodically to pick up newer versions intentionally.

## What this is not

* **Not a skill creator.** This repo holds the gates that catch drift. Authoring guidance lives in [`docs/authoring-walkthrough.md`](docs/authoring-walkthrough.md), but the actual skill content is yours.
* **Not a taxonomy.** `categories.yaml` declares the categories your repo uses. There is no shared catalog. The example in [`examples/categories.yaml`](examples/categories.yaml) is a starting point, not a prescription.
* **Not opinionated about voice.** The handbook documents one set of voice conventions in section 6 because the rest of the document follows them, but none of those rules are validator-enforced. Adopt or replace as your project prefers.

## Requirements

* Python 3.10+ in the consumer repo. PyYAML is installed automatically by `pre-commit` into the hook's isolated venv.
* [`pre-commit`](https://pre-commit.com/) installed in the consumer repo.

Local development of the hooks themselves (this repo): `pip install -e .` puts `validate-skills`, `check-dead-links`, and `check-commit-closes-issue` on `$PATH`.

## License

MIT. See [`LICENSE`](LICENSE).

## See also

- [AGENTS.md](AGENTS.md) - agent-facing operating rules.
- [docs/FEATURES.md](docs/FEATURES.md) - inventory of what ships today.
- [.coily/coily.yaml](.coily/coily.yaml) - allowlisted commands. Agents route through coily, not bare `make` / `uv` / `python` / `npm` / `cargo` / `dotnet`.

Cross-reference convention from [coilysiren/agentic-os-kai#313](https://github.com/coilysiren/agentic-os-kai/issues/313).
