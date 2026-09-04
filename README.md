# sdd-playground

> A sandbox for exercising Spec Driven Development end to end, for whoever wants
> to see how the loop behaves before running it on something that matters.

Features built here are vehicles for testing the method, not a product. Judge
them by whether the loop held, not by whether anyone would ship them.

**Status:** prototype — no application code yet.

---

## Getting started

### Prerequisites

- [uv](https://docs.astral.sh/uv/) — dependency management and running
- Python — version pinned in `pyproject.toml` once the first spec creates it

### Install and run

```bash
uv sync
```

There is no run command yet: nothing has been built. The first `/sdd-plan` picks
the entry point, and it lands here.

## Commands

| Task | Command |
| --- | --- |
| Test | `uv run pytest` |
| Lint | `uv run ruff check .` |
| Format | `uv run ruff format .` |

## Layout

```text
docs/specs/            one directory per feature — spec.md · plan.md · tasks.md
docs/commands/         the workflow behind each /sdd-* command
```

## Spec first

> [!IMPORTANT]
> **No code without an approved spec.** Every feature starts in
> `docs/specs/NNN-slug/`, and the agent stops for approval **after the spec** and
> **after the plan**.

```text
/sdd-specify → /sdd-plan → /sdd-tasks → /sdd-implement
     WHAT          HOW         work         one task per run
```

Trivial changes skip the spec; for a bug fix or a small tweak the agent asks
first rather than assuming.

| Where | What is in it |
| --- | --- |
| `AGENTS.md` | The rules every agent follows |
| `docs/constitution.md` | The durable principles |
| `docs/delivery.md` | Branches, feature flags, releases |
| `docs/commands/` | The workflow behind each command |
| `docs/specs/NNN-slug/` | One feature: `spec.md` · `plan.md` · `tasks.md` |

> [!TIP]
> **New here?** Set up your agent tool once with `docs/commands/onboard.md` — the
> adapter and command files are generated per developer, not committed.

## Contributing

| | |
| --- | --- |
| Branch from | `develop` — `feature/<NNN>-<slug>` or `fix/<NNN>-<slug>` |
| Pull request title | Conventional Commits — `feat(007): send the reset email` |
| Merge | squash into `develop`; `develop` → `main` promotes to production |
| Release | automatic from `main`: a SemVer tag and a GitHub Release |

Work too large for one merge reaches `main` behind a feature flag, off, and is
switched on afterwards. Full rules in `docs/delivery.md`.
