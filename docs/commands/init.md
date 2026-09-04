# /sdd-init — prepare a repository for the loop

**Goal.** Leave the repository ready to run `/sdd-specify` → `/sdd-plan` →
`/sdd-tasks` → `/sdd-implement`: `AGENTS.md`, `docs/constitution.md` and
`README.md` filled with what is true about *this* project, and nothing else
invented. Runs once per repo.

Agent tooling is not this command's job — that is `/sdd-onboard`, run once per
developer. This one touches no adapter or command files.

Argument: `new` (no code yet) or `existing` (there is a codebase). If it is
missing, decide by looking — a repository with source files is `existing`.

## Read first

Always: `AGENTS.md`, `docs/constitution.md`, `README.md`,
`docs/templates/readme.md`.

`existing` only, before asking anything:

- The README the project had **before** the template was copied in — the current
  one documents the template. `git show HEAD:README.md`, or the earlier revision
  `git log --oneline -- README.md` points at.
- Package manifests and lockfiles, `Makefile`, task runners, `scripts/`.
- CI workflows — they hold the commands that actually run.
- How it already ships: `git branch -r`, `git tag --list`, `CHANGELOG.md`, the
  branch protection you can see, deploy configuration.
- Linter, formatter and test configuration.
- The directory layout, and the two or three largest source directories.
- Any existing agent file, `CONTRIBUTING.md`, `docs/`, ADRs.
- **A previous spec system**, if there is one: `.specify/`, `.kiro/specs/`,
  `memory/constitution.md`, an `rfcs/` or `adr/` directory, a `docs/features/`
  full of feature documents. Its rules and constitution are an input to this
  command; its specs are not — those are `/sdd-adopt`, one at a time.

## Ask only

What the repository cannot tell you. Ground every command, convention and
boundary in a file you read, and name that file when you report it. What you
cannot ground is a question — never a plausible `npm test` for a project that
has no tests.

- `new` — what it is and who for; the stack, if it is already chosen; how it will
  be run and tested; what is off the table.
- `existing` — what you could not derive, then the one thing that is never
  derivable: *which conventions do you want to start enforcing that the code does
  not follow yet?* Existing patterns are evidence of habit, not of intent.
- Both — the project constraints for the constitution: platforms, performance,
  security, compliance, data residency. If there are none, say so.
- Both — whether the project has a user interface, and at which breakpoints. For
  `existing` the dependencies and the layout usually answer it; for `new` it is a
  question. It decides whether a spec gets a wireframe.
- Both — whether the product spans repositories that cannot be built or verified
  from here. It decides whether `docs/cross-repo.md` is created.
- Both — how this project reaches production: is it on GitHub Actions, is there
  a `develop` branch, and does the team already follow a branching model? It
  decides what `docs/delivery.md` has to change and whether
  `.github/workflows/` stays. A documented model nobody follows is worse than no
  document.

## Steps

1. Read. For `existing`, report what you found *before* asking anything — the
   user corrects your reading of the repo more cheaply than your questions.
2. Ask, in one round where possible.
3. Fill `AGENTS.md`: Project, Commands, Conventions, Boundaries, Done means.
   Delete every `<...>` you cannot fill; an unfilled placeholder is paid for on
   every turn. Never touch the Rule 1 block, its `<!-- ssd:rule1:* -->`
   delimiters, or the "Read on demand" table. Keep the file at 60 lines or fewer.
   The `Interface` line is the one placeholder to fill rather than delete when it
   does not apply: `/sdd-specify` reads it, and `none` is an answer.
4. Fill `## Project constraints` in `docs/constitution.md`, or delete that
   section. Leave the five principles and the Amendments section alone — changing
   a principle needs the user's explicit approval and an Amendments entry.
5. Rewrite `README.md` from `docs/templates/readme.md`. The one that ships with
   the template documents *the template* — leaving it is how a repo ends up
   describing the wrong project. Same answers as `AGENTS.md`, aimed at a human
   arriving at the repository rather than at an agent: what it is, how to run it,
   where the specs live. Delete every `<...>` you cannot ground; a plausible
   install command nobody has run is worse than a missing section. Keep the
   "Spec first" section either way — it is the only part that is about the loop,
   and it is what tells a newcomer why `docs/specs/` exists.
   For `existing`, the project's own README is the source, not the template:
   carry over what it already said — install steps, usage, badges, license,
   links — and add only the sections it was missing.
6. `existing` only: keep conventions that are already documented elsewhere. If
   one applies to every task it belongs in `AGENTS.md`; otherwise leave it in
   `docs/` and give it a row in the "Read on demand" table.
7. If the product spans repositories that cannot be built from here, copy
   `docs/templates/cross-repo.md` to `docs/cross-repo.md` — that is where the
   loop's other commands look for it. A single-repo product does not copy it.
8. Adapt `docs/delivery.md`. It ships with the model this template recommends —
   `feature/* → develop → main → deploy → flag on`, no release branches — and
   that is a default, not a fact about this repo. Three things to settle:
   - No `develop` branch yet: say so, and give the one command that creates it.
   - Not on GitHub Actions: delete `.github/workflows/` and name the CI that
     runs the same two checks. A workflow for a CI nobody uses is worse than no
     workflow.
   - A branching model the team already follows: write down theirs, and keep
     `## Feature flags` — it holds whatever the branches look like.
   Never invent a deploy target. "Not decided yet" is an answer; a fictional
   pipeline is not.
9. Delete the example spec that ships with the template, and any other spec
   directory that is not this project's. Delete `docs/templates/readme.md` too —
   it is spent, the README is written. With no user interface, delete
   `docs/templates/wireframe.html` as well — nothing will read it. Single-repo
   product: delete `docs/templates/cross-repo.md`, it was not needed.
   `docs/delivery.md` is not a template — it stays, adapted in step 8.
   Delete `CHANGELOG.md` if it carries the template's own releases: a repository
   created from a template inherits it, and a project whose changelog opens with
   someone else's versions is lying from its first line. The first release here
   writes a new one.
10. If you found a previous spec system, list its specs and stop there. Do not
    convert them — say how many there are and that `/sdd-adopt <path>` takes them
    one at a time.

## Writes

`AGENTS.md`, `docs/constitution.md`, `README.md`, `docs/delivery.md`, and
`docs/cross-repo.md` when the product spans repos. It deletes the inherited
`CHANGELOG.md`, and `.github/workflows/` when the project is not on GitHub
Actions. No source code,
no scaffolding, no first spec, no agent adapters, and no conversion of anyone
else's specs.

## Stops when

`AGENTS.md`, `docs/constitution.md` and `README.md` are written. Report what you
grounded in which file, what came from the user's answers, which README sections
you left out for lack of an answer, and which branch the first pull request
should target. Next: `/sdd-specify`, or
`/sdd-adopt` if there are specs worth carrying over.
