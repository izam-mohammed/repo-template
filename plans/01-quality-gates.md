# a change to the template is linted, and its pull request runs the same check as a laptop

## The ask

Every repository should have lint, tests, a coverage floor and security
checks run by one local check command and by CI on every pull request,
reusing the shared gate in `izam-mohammed/.github` (brief of 22/10/2026).
This repository is the starting point of every new one, so its own bar is
the first thing to fix: today it runs the security gate and pre-commit, has
no check command, pins the gate at its first commit and has a Dependabot job
that fails on every run.

Built so far: `docs/built/BUILT-2026-10-22-1828.md`.

Coverage: not applicable to the template itself. It holds configuration
and no code; `plans/02-language-gate.md` adds the test that matters here,
that a repository made from it passes its own check, with coverage, in each
language.

## Alternatives

### Decision 1: the one check command (two-way door)

Searched 2026-10-22: "just command runner vs make for project task
runner"; the Atomic Object and Linux Magazine pieces on just, and
`project-setup`'s `SKILL.md` step 8 (same target names in every repo).

| Criterion | Baseline: `pre-commit run --all-files` by hand | `make check` | just |
| --- | --- | --- | --- |
| Performance efficiency | instant | instant | instant |
| Fit to the ask | misses workflow lint | runs every check | runs every check |
| Compatibility | no target names | the house target names | a new binary everywhere |

**Chosen: `make check`.** Same target names as every repository made from
the template. Revisit if the house style moves off Make.

### Decision 2: what lints the template's workflows (two-way door)

Searched 2026-10-22: "actionlint vs zizmor GitHub Actions workflow linting
security"; the WordPress workflow standards (both tools), zizmor's docs.

| Criterion | Baseline: the gate's zizmor stage, reporting only | actionlint and zizmor in `make check` |
| --- | --- | --- |
| Performance efficiency | runs only on GitHub | under 2 s locally |
| Fit to the ask | security only, never blocks | correctness and security, blocks |
| Reliability | a wrong reusable-workflow input reaches every new repository | caught before merge |

- zizmor 1.31.1 here on 22/10/2026: 0 findings, 1 suppressed.

**Chosen: actionlint and zizmor in `make check`.** The template's
workflows are copied into every new repository, so a broken one is
broken everywhere. Revisit never.

### Decision 3: where CI runs the check (two-way door)

Searched 2026-10-22: "reusable workflow vs composite action GitHub Actions
differences 2026"; GitHub's docs on reusing workflows.

| Criterion | A local `check.yml` running `make check` | `quality.yml` from `izam-mohammed/.github` |
| --- | --- | --- |
| Compatibility | a second pattern beside the shared one | the same caller every new repository gets (`@.github/plans/02-language-gate.md`) |
| Fit to the ask | runs the check | runs the check and shows coverage "n/a, no code" visibly |
| Availability | now | once `.github` lands `quality.yml` |

**Chosen: a local `check.yml` now, switched to `quality.yml` in
`plans/02-language-gate.md`.** The template should not wait on the gate to
get a check; the switch is one file. Revisit when `quality.yml` lands.

## The decision

- `make check` runs `pre-commit run --all-files`, actionlint and zizmor
  over `.github/workflows/` (decisions 1 and 2), tools fetched at pinned
  versions to a cache when missing.
- `.github/workflows/check.yml` runs `make check` on every pull request and
  push to `main` (decision 3).
- The gate pin moves from `ce1b7cc` to the current `izam-mohammed/.github`
  main (`ff8da46` on 22/10/2026, or the `v0.2.0` tag if it exists by then),
  with the version in a trailing comment so Dependabot can bump it.

New dependencies: actionlint and zizmor, pinned, in the check only. New
permission: `contents: read` for `check.yml`. No secret.

Standards: `docs/standards.md` (this plan replaces "no check command" with
`make check` in the same phase).

Security: the only new input is the template's own pull requests, which run
read-only on `pull_request`; the tools are pinned; nothing else applies.

## Where it lands

| Path | New or edit | Phase | What changes | Firm? |
| --- | --- | --- | --- | --- |
| `Makefile` | new | 1 | `check`, `lint` targets | fixed |
| `.github/workflows/check.yml` | new | 2 | runs `make check` | expected |
| `.github/workflows/security.yml` | edit | 2 | pin bumped, version comment | reuse |
| `docs/standards.md` | edit | 1 | the check command and CI | reuse |

*Reused, not rewritten:*

- `.pre-commit-config.yaml`: run whole inside `make check`.
- `@project-setup/templates/Makefile.python` `security` and `lint` targets:
  the same tool flags; target names kept identical.
- `@.github/plans/01-quality-gates.md`: the same actionlint and zizmor
  pins, so the template and the gate lint alike.

## Declined

- pre-commit by hand: misses workflow correctness (decision 1).
- just: a new binary in every new repository (decision 1).
- The gate's zizmor stage alone: reports only, after merge matters
  (decision 2).

## What the tests hold

`make check` fails on a workflow with a wrong `needs:` name (actionlint) and
on an unpinned action (zizmor); the PR's `check` job runs the same command.
The template-instantiation tests land in `plans/02-language-gate.md`.

## Follow-ups

- The Dependabot `uv` job fails on the template ("No files found in /");
  the fix belongs with the per-language Dependabot files in
  `plans/02-language-gate.md`, so a new Python repository keeps its `uv`
  updates.

## Repository knowledge

Read root `KNOWLEDGE.md` when present before each phase. Follow the global
and repository instructions for recording useful development findings.

## Phases

### Phase 1: the rough cut, `make check` on a laptop (needs you: try it)

- [ ] `make check` runs pre-commit, actionlint and zizmor (expected in
  `Makefile`), with tool versions pinned at the top of the file.
- [ ] `docs/standards.md` names `make check` and its rows.
- [ ] Check: `make check` passes on a clean clone.
- [ ] You try it: run `make check`, then unpin an action in
  `.github/workflows/security.yml` and run it again; say whether the
  failure reads clearly.

### Phase 2: CI runs it, and the gate pin is current

- [ ] `.github/workflows/check.yml` runs `make check` on pull requests and
  pushes to `main`, read-only, actions pinned by SHA.
- [ ] `.github/workflows/security.yml` pins the current `.github` commit
  with its version comment.
- [ ] Check: `make check` passes, and the PR's `check` and `Security` jobs
  are green.

### Phase 3: open the PR

- [ ] The branch pushed and its PR opened against the branch it was cut
  from (or, if one is already open for this branch, its body brought up to
  date) with a Conventional Commit title for the whole change, a body
  naming this plan, what changed and why, and the checks run with their
  result. The PR's URL printed as the evidence. Opened, never merged.
