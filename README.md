# SDD Template — Spec Driven Development

> **Rule #1 — Spec First:** no code without an approved spec.

A starting point for projects that work that way. Frontend, backend, mobile, data
or CLI — the structure is the same, only the placeholders change.

> [!NOTE]
> **This file is for humans. Agents read `AGENTS.md`.**
> It documents *the template*, so `/sdd-init` replaces it with a README for your
> own project, written from `docs/templates/readme.md`.

**Contents**

[The artifacts](#the-artifacts) · [The loop](#the-loop) ·
[Bootstrap a new project](#bootstrap-a-new-project) ·
[Working a feature](#working-a-feature) · [Shipping](#shipping) ·
[Layout](#layout) · [Any agent](#any-agent) ·
[Coming from another spec system](#coming-from-another-spec-system) ·
[Wireframes](#wireframes) · [One US, several repos](#one-us-several-repos) ·
[Why `AGENTS.md` stays under 60 lines](#why-agentsmd-stays-under-60-lines)

---

## The artifacts

Four files per feature, each answering exactly one question:

| Artifact | Answers | Scope |
| --- | --- | --- |
| `spec.md` | **WHAT** the product must do, and why | the product — the same spec in every repo |
| `plan.md` | **HOW** *this* repo implements it | this repo |
| `tasks.md` | the ordered, commit-sized units of work | this repo |
| `wireframe.html` | **where things sit** on screen | only features with screens |

## The loop

```text
setup        onboard ──────────── your agent tool's files      once per developer
             /sdd-init ────────── AGENTS.md + constitution.md  once per repo
─────────────────────────────────────────────────────────────────────────────
per feature  /sdd-specify ─────── spec.md          ← STOP, approval gate
             /sdd-plan ────────── plan.md          ← STOP, approval gate
             /sdd-tasks ───────── tasks.md
             /sdd-implement ───── code, one task per run
```

Full sequence, optional steps included:

| # | Command | What it does | Notes |
| --- | --- | --- | --- |
| 1 | `onboard` | Your agent tool's adapter and command files — follow `docs/commands/onboard.md`, there is no command yet | once per developer |
| 2 | `/sdd-init new\|existing` | Fills `AGENTS.md` and `docs/constitution.md` | once per repo |
| 3 | `/sdd-adopt <path>` | Carries a spec over from a previous system | optional |
| 4 | `/sdd-specify` | WHAT and WHY (+ wireframe, if it has screens) | |
| 5 | `/sdd-clarify` | Closes what the spec leaves ambiguous | optional |
| 6 | `/sdd-plan` | HOW this repo builds it — the technical decisions | |
| 7 | `/sdd-tasks` | Ordered, commit-sized units, for this repo only | |
| 8 | `/sdd-analyze` | Cross-checks spec, plan and tasks | optional |
| 9 | `/sdd-implement` | One task per run, then verify | |

> [!IMPORTANT]
> **Two approval gates: after the spec, and after the plan.** The agent stops at
> both, and `/sdd-plan` refuses to run on a spec that is not `approved`.

**A command is an interactive workflow, not a canned prompt.** Each one:

1. reads the repo and the existing artifacts first,
2. asks only what it cannot work out from them,
3. writes its artifact, and stops.

The workflows live in `docs/commands/`, one file per command, and that file is
the whole definition — the per-tool command files are pointers into it, generated
by `onboard`.

They carry the `sdd-` prefix so they never collide with a tool's own commands
(`/init` and `/tasks` are already taken in some agents) and so the whole loop
groups together in autocomplete.

## Bootstrap a new project

1. **Copy this template** into the new repo.
2. **Set up your agent tool.** Point it at `docs/commands/onboard.md` and follow
   it. It asks which tools you use and generates their adapter and `sdd-*`
   command files — all `.gitignore`d. After this, `/sdd-init` and the rest work
   as slash commands. *Each developer does this once.*
3. **Run `/sdd-init new`** if there is no code yet, or **`/sdd-init existing`**
   if there is. It reads what is already there, asks only what it cannot work
   out, fills `AGENTS.md` and `docs/constitution.md`, replaces this README with
   one that describes your project, and deletes the example spec.
4. **Run `/sdd-specify <what you need>`** for the first feature.

<details>
<summary><b>Prefer to do steps 2–3 by hand?</b> Just as valid — the commands only save you the questions.</summary>

<br>

- **Onboarding** — `docs/commands/onboard.md` spells out every file to write.
- **Init** — fill the `<...>` placeholders in **`AGENTS.md` only**, deleting the
  lines that don't apply.
- Fill or delete `## Project constraints` in `docs/constitution.md`, leaving the
  five principles alone.
- Replace this README with `docs/templates/readme.md` filled in. On an existing
  repo, keep what your own README already said and only add the sections it
  lacked.
- Delete every directory under `docs/specs/` — they ship as examples, not as your
  specs.

</details>

## Working a feature

| Step | You | Agent |
| --- | --- | --- |
| 1 | `/sdd-specify <feature>` | Asks what's missing, writes `spec.md` (+ `wireframe.html` if it has screens), stops |
| 2 | `/sdd-clarify` if anything is still open | Asks, writes the answers into `spec.md` |
| 3 | Review and approve (`status: approved`) | — |
| 4 | `/sdd-plan <NNN>` | Writes `plan.md`, stops |
| 5 | Approve the approach | — |
| 6 | `/sdd-tasks <NNN>` | Writes `tasks.md`, stops |
| 7 | `/sdd-analyze <NNN>` when it earns its keep | Reports what doesn't line up; edits nothing |
| 8 | `/sdd-implement <NNN>`, once per task | One task per run, then verifies the acceptance criteria |

Steps 2 and 7 are optional and meant to be skipped when they'd find nothing.
Every command reads the current state first, so a spec left half-finished resumes
where it stopped instead of starting over.

### Does this change need a spec?

Not every change is a feature:

| Change | What happens |
| --- | --- |
| Typo, dependency bump, edit to the SDD scaffolding itself | Skips the spec |
| Bug fix, copy change, refactor | The agent **asks** whether to spec it, rather than assuming |
| New feature, or any change to WHAT the product does | Spec required |

`Rule 1` in `AGENTS.md` is the triage it follows.

> [!WARNING]
> When implementation reveals the spec was wrong: **stop and update the spec**,
> then continue. Never let the code silently redefine what was agreed.

## Shipping

Two permanent branches, and nothing else:

```text
feature/007 ─┐
fix/012 ─────┴─▶ develop ─▶ main ─▶ deploy ─▶ flag on
```

| Branch | Holds |
| --- | --- |
| `main` | production |
| `develop` | everything integrated, waiting to be promoted |
| `feature/*` `fix/*` `hotfix/*` | one spec's work, deleted on merge |

**There are no release branches.** Work too big for one merge reaches `main`
turned **off** behind a feature flag instead, so deployment and activation are
two separate events — and that separation is the only reason the branching can
stay this short. A release branch is a second `main` you have to keep in step
with the first, and everything people hate about git flow (forward-porting,
cherry-pick trains, versions that drift) is the price of keeping it.

Pull request titles follow Conventional Commits, merges are squashes, and
`.github/workflows/release.yml` derives the next SemVer tag from those titles
and cuts the GitHub Release from `main`. Nobody types a version number.

The flag is a `plan.md` decision and its removal is a task in `tasks.md`, the
same way a mock written against a contract gets its own removal task.

Full rules — the flag pattern, what keeps flags from becoming debt, the two
workflows and the repository settings they assume — in `docs/delivery.md`.

## Layout

```text
AGENTS.md              source of truth — the only file loaded every session
README.md              this file — /sdd-init replaces it with your project's own
.gitignore             keeps the per-developer agent files out of version control
.github/
  workflows/           pr-title.yml · release.yml — the delivery model, enforced
docs/
  commands/            the nine workflows, one file per command (incl. onboard)
  constitution.md      durable principles; read when a spec is silent
  delivery.md          branches, feature flags, versioning, releases
  templates/           spec.md · plan.md · tasks.md · wireframe.html
                       readme.md — the project README, written by /sdd-init
                       cross-repo.md — /sdd-init copies it in for multi-repo products
  specs/
    001-user-login/    one directory per feature
      spec.md
      plan.md
      tasks.md
      wireframe.html   only when the feature has screens
```

That is the whole repo. `onboard` adds, **outside version control**, the adapter
your tool needs and its `sdd-*` command files.

Specs are numbered `NNN-slug`, zero-padded, **never reused**. The number is the
permanent id you reference from commits, branches and issues —
`feature/007-password-reset`, `feat(007): …`. See `docs/delivery.md`.

## Any agent

Claude Code, Copilot, Antigravity, Cursor, Codex, Gemini CLI, Windsurf, Zed,
Aider — the method does not care which one you use.

**`AGENTS.md` is the source of truth.** The template ships nothing tool-specific:
most agents read `AGENTS.md` natively, and the four that need an adapter get a
thin one that points at `AGENTS.md` and repeats only the `Rule 1 — Spec First`
block.

| Tool | Adapter | Command files |
| --- | --- | --- |
| Claude Code | `CLAUDE.md` | `.claude/` |
| Cursor | `.cursor/rules/00-spec-first.mdc` | `.cursor/` |
| GitHub Copilot | `.github/copilot-instructions.md` | `.github/prompts/` |
| Antigravity / Gemini CLI | `GEMINI.md` | `.gemini/` |

Those adapters and the per-tool command files are **generated per developer** by
`docs/commands/onboard.md` and are **`.gitignore`d** — they carry no project
content, so they belong to whoever is using that tool, not to the repo.

> [!TIP]
> A team that *does* want to share one deletes its line from `.gitignore` and
> commits it.

## Coming from another spec system

`/sdd-init existing` reads whatever rules and constitution you already have and
folds them into `AGENTS.md` and `docs/constitution.md`.

Your existing specs are a separate job: **`/sdd-adopt <path>`**, run once per
spec, converts one of them into `docs/specs/NNN-slug/`.

It triages before it converts, and that is the point:

| Spec state | What `/sdd-adopt` does with it |
| --- | --- |
| Already shipped | Archived, rather than back-filled with acceptance criteria nobody wrote |
| Not yet started | Usually better re-run through `/sdd-specify`, with the old document as input |
| **In flight** | The only kind worth converting |

Nothing that is missing from the original gets invented to fill our template.

## Wireframes

A feature with screens gets a fourth artifact: `wireframe.html`, written by
`/sdd-specify` next to `spec.md`. It holds one section per screen, that screen
drawn at each breakpoint side by side, and the empty and error states next to the
happy one.

**It answers where things sit, and nothing else.** Grayscale and hand-drawn on
purpose: a wireframe that looks finished gets reviewed as a design, and the
layout is the part that is still cheap to change. Every element traces back to a
requirement — a control nobody can justify is a hole in the spec, not a detail of
the picture. On any discrepancy **the spec wins** and the wireframe is corrected.

The file opens in any browser straight from disk: no build step, no runtime, no
network.

| Command | Its relationship to the wireframe |
| --- | --- |
| `/sdd-clarify` | Keeps it in step when an answer changes a screen |
| `/sdd-analyze` | Reports where it has drifted |
| `/sdd-implement` | **Does not read it** — implementation follows `plan.md`, or the wireframe quietly becomes a HOW specification |

Projects with no user interface never see any of this: `/sdd-init` records the
answer in `AGENTS.md`, and `/sdd-specify` reads it before writing anything.

## One US, several repos

A product story is often transversal while its code is not. The rule is **one
spec for the product, one plan and one task list per repo**:

- **`spec.md`** — WHAT the product must do, independent of technology and
  repository. The same spec sits in every repo that implements it.
- **`plan.md`** — HOW **this** repo implements it, backend or frontend, and which
  part of the spec it takes.
- **`tasks.md`** — only the tasks needed to implement it **here**.

```text
US-4417                             ← one spec, the same in all three
├── web-app   007-password-reset   consumer
├── api-svc   012-password-reset   OWNER      ← defines the contract
└── infra     004-password-reset   consumer
```

Same US id and slug everywhere, local `NNN`, contract copied verbatim from the
owning repo. The split between repos is drawn in `plan.md` → `## Scope in this
repo`, so no repo is ever asked to verify a criterion it cannot reach.

Full rules in `docs/cross-repo.md` — `/sdd-init` copies it in from
`docs/templates/` when the product spans repos.

## Why `AGENTS.md` stays under 60 lines

`AGENTS.md` is loaded into context on **every** session and turn — it is the only
file with a permanent cost. Everything else is loaded on demand through the
"Read on demand" table at the bottom of it.

| Guidance needed… | Goes in |
| --- | --- |
| on every task | `AGENTS.md` |
| only on *some* tasks | `docs/`, plus a row in the "Read on demand" table |

When the file grows past ~60 lines, that is the signal something in it belongs in
`docs/` instead.

The adapters make that stricter, not looser. Whatever sits in `AGENTS.md` is paid
for in every tool, on every turn — and each adapter repeats the `Rule 1` block
verbatim, so the budget is the same 60 lines, enforced harder.
