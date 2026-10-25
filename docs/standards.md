# repo-template: what every change passes

Run everything: there is no single check command yet.
`plans/01-quality-gates.md` lands `make check` and CI that runs it on every
pull request. Until then a change passes when the checks below are clean.

Recorded on 22/10/2026 from what the repository already runs; nothing here
is new.

## Toolchain

| What | Version | Pinned in |
| --- | --- | --- |
| pre-commit hooks | gitleaks v8.30.1, pre-commit-hooks v6.0.0, ruff-pre-commit v0.16.3 | `.pre-commit-config.yaml` |
| Shared security gate | `izam-mohammed/.github` at `ce1b7cc` | `.github/workflows/security.yml` |

## Checks

| Check | Tool | Config | Command | Passing means |
| --- | --- | --- | --- | --- |
| Secret scan (local) | gitleaks via pre-commit | `.pre-commit-config.yaml` | `pre-commit run --all-files` | no findings |
| Hygiene (local) | pre-commit-hooks | `.pre-commit-config.yaml` | `pre-commit run --all-files` | no findings |
| Python lint and format (for repos made from it) | ruff via pre-commit | `.pre-commit-config.yaml` | `pre-commit run --all-files` | no findings (no Python files here) |
| Security gate (PR and weekly) | gitleaks (blocking), osv-scanner, semgrep, zizmor (reporting) | `.github/workflows/security.yml` | runs in CI | stage 1 green; last scheduled run green on 19/10/2026 |
| Workflow security | zizmor | none | `zizmor .github/workflows` (by hand) | 0 findings, 1 suppressed, zizmor 1.31.1 on 22/10/2026 |
| Updates | Dependabot | `.github/dependabot.yml` | weekly | the `uv` job fails here ("No files found in /", run of 15/10/2026), as the template has no `uv.lock` |

## Tests

None. Coverage: not applicable to the template itself, which holds
configuration and no code. What it must prove is that a repository made
from it passes its own check in each language: that test is planned in
`plans/01-quality-gates.md`.

## Constraints

- A GitHub template repository: every file here is copied into each new
  repository, so anything added must be right for the new repository, or
  removed by its setup step.
- Two values must be edited by hand after creation today: `ecosystem` in
  `.github/workflows/security.yml` and `package-ecosystem` in
  `.github/dependabot.yml` (`README.md`).

## Conventions

- Commits: lowercase Conventional Commits (`ci: ...`, `docs: ...`); no
  attribution lines.
- README voice: casual, first person, `~` asides (`README.md`); keep it in
  README-facing text.

## CI

`.github/workflows/security.yml` calls the shared gate on every pull
request and weekly (Monday 03:17 UTC). No lint or test job.

## Decided

- The shared gate over a local copy of it (`6afe3ec`).
- ruff in pre-commit as the Python default (`6afe3ec`).
