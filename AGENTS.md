# AGENTS.md — sdd-playground

**Source of truth for every agent.** Tool adapters (`CLAUDE.md`, `GEMINI.md`, …) are generated
per developer by `docs/commands/onboard.md`, only point here, and hold no project content.

<!-- ssd:rule1:start -->
## Rule 1 — Spec First

No code without an approved spec. But first, classify the request:

- **No spec — just do it:** typo/wording fix, dependency bump, formatter output, comments, or a change to the SDD scaffolding itself (`AGENTS.md`, `docs/`, templates, agent command/skill files).
- **Ask first — never assume:** bug fix, copy change beyond a typo, refactor, config tweak, or anything you are unsure about. Ask the user *"spec this, or handle it as a no-spec change?"* and follow the answer — in doubt, ask; do not default to writing the spec.
- **Spec required:** a new feature, or any change to WHAT the product does — user-visible behaviour, a contract, a data model. Then:

1. Find the spec in `docs/specs/` — WHAT the product does, independent of stack and repo. If none exists, write it from `docs/templates/spec.md` and STOP for approval.
2. Follow that spec's `plan.md` — HOW **this** repo implements it — and its `tasks.md`, the work to do **here** and nothing else. Missing? Create from templates, STOP for approval.
3. Implement one task at a time, checking it off in `tasks.md`. Spec and code disagree → STOP and ask; never edit the spec to match the code.
<!-- ssd:rule1:end -->

## Project

A sandbox for exercising the SDD loop end to end. Features built here are
vehicles for testing the method, not a product — judge them by whether the loop
held, not by whether anyone would ship them. Python, run with `uv`.
Interface: none. Specs here do not get a wireframe.

## Commands

- Test: `uv run pytest`
- Lint: `uv run ruff check .`
- Format: `uv run ruff format .`
- Run: none yet — no code. The first `/sdd-plan` picks the entry point.

## Conventions

- Language/version: Python, pinned in `pyproject.toml` once it exists
- Naming: PEP 8 — `snake_case` for modules and functions, `PascalCase` for classes
- Dependencies go through `uv add`, never a hand-edited `pyproject.toml`

## Boundaries

- Never touch: `uv.lock` by hand, `.venv/`, secrets
- Ask before: adding a dependency, changing a public API

## Done means

- [ ] Acceptance criteria in `spec.md` met — the ones `plan.md` scopes here
- [ ] `uv run pytest` passes and `uv run ruff check .` is clean
- [ ] `tasks.md` updated

## Read on demand (not upfront)

| Need | File |
| --- | --- |
| Running a stage of the loop | `docs/commands/` |
| Principles, trade-off rules | `docs/constitution.md` |
| Active spec | `docs/specs/<NNN-slug>/` |
| Artifact structure | `docs/templates/` (incl. `wireframe.html`) |
| Branching, flags, releases | `docs/delivery.md` |
| US spanning several repos | `docs/cross-repo.md` (multi-repo projects only) |
