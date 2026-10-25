# every repo made from the template starts with working updates, a full security policy and a licence

## The ask

Every repository gets Dependabot and a proper `SECURITY.md`, and every
repository that is a product, private ones included, gets a proper licence.
This repository is the template every new repository is created
from, so its own `.github/dependabot.yml` and `SECURITY.md` are also the
starting point for every repository made from it.

Read on `origin/main` (`426e211`) on 22/10/2026:

- `.github/dependabot.yml` has a `uv` block and a `github-actions` block,
  no cooldown and no groups. The `uv` block fails on the template itself,
  because the template has no `pyproject.toml` or `uv.lock` (recorded in
  the open PR #1, `docs/built/BUILT-2026-10-22-1828.md` on
  `plan/repo-standards`).
- `.pre-commit-config.yaml` pins gitleaks, pre-commit-hooks and
  ruff-pre-commit by `rev`, and nothing updates those pins.
- `SECURITY.md` has a reporting link and a table of controls, but no
  supported versions, no "what happens next", no scope, nothing for a
  private repository (where GitHub private vulnerability reporting is not
  offered), and it uses em dashes.
- There is no `LICENSE`.

The open PR #1 (`plans/02-language-gate.md`) plans to move per-language
Dependabot blocks into starters later; this plan does not wait for it and
leaves the template in a state that plan can build on.

## Alternatives

### Decision 1: which update bot (two-way door)

Something has to open update PRs for the actions, the pre-commit hooks
and, in a copied repository, its language's packages.

Searched 22/10/2026: "dependabot vs renovate", "dependabot pre-commit
ecosystem", "dependabot cooldown"; read the GitHub Docs pages
"Dependabot options reference" and "Supported ecosystems and
repositories", and the Renovate docs "Installing and onboarding".

| Must | Baseline: Dependabot | Renovate |
| --- | --- | --- |
| Runs with no app or server to install per repository | yes | no (needs the Renovate GitHub app or a self-hosted runner) |
| Covers github-actions and pre-commit | yes | yes |
| **Survives** | **yes** | no |

| Criterion | Weight | Baseline: Dependabot |
| --- | ---: | ---: |
| Performance efficiency | 40 | 4 |
| Reliability | 30 | 4 |
| Compatibility | 30 | 5 |
| **Weighted total** | **100** | **4.30** |

- Dependabot compatibility 5: already configured here and in the shared
  security gate, which reads Dependabot alerts.
- Efficiency 4: one grouped PR per ecosystem per week, with a 7 day
  cooldown (`cooldown: default-days: 7`, options reference).

Sensitivity: only a need Dependabot cannot meet (an unsupported manager)
would reopen this. Risk: a broken block fails silently in the Dependabot
log; limited by keeping only blocks whose manifests exist.

**Chosen: Dependabot.** It is native, already wired, and covers every
ecosystem here. Revisit if a copied repository needs a manager Dependabot
does not support.

### Decision 2: which licence (one-way door for published copies)

Searched 22/10/2026: "MIT vs Apache-2.0 for templates", choosealicense.com
MIT and Apache-2.0 pages, the SPDX list.

| Must | MIT | Apache-2.0 |
| --- | --- | --- |
| OSI approved, lets anyone copy the template | yes | yes |
| **Survives** | **yes** | **yes** |

| Criterion | Weight | MIT | Apache-2.0 |
| --- | ---: | ---: | ---: |
| Performance efficiency | 40 | 5 | 3 |
| Compatibility | 30 | 5 | 4 |
| Reliability | 30 | 3 | 5 |
| **Weighted total** | **100** | **4.40** | **3.90** |

- Efficiency: MIT is one short file with no NOTICE handling for copiers;
  Apache-2.0 asks copiers to carry a NOTICE and mark changed files.
- Compatibility: MIT is compatible with GPLv2 and Apache-2.0 is not.
- Reliability: Apache-2.0 has an express patent grant; MIT does not.

Sensitivity: weighting reliability at 50 would tie them; a template of
config files carries no patents, so it holds.

**Chosen: MIT**, as the owner listed for this repository. Revisit if the
template ever ships code with patentable ideas.

## The decision

- `.github/dependabot.yml` drops the failing `uv` block, adds
  `pre-commit` for the hook pins, and gives every block a weekly
  schedule, a 7 day cooldown, one group and the `ci(deps)` or
  `chore(deps)` prefix (decision 1). A commented block shows the line a
  new repository uncomments for its language.
- `SECURITY.md` becomes a starting point that is right for both a public
  and a private copy: supported versions, how to report (private
  vulnerability reporting, or the owner privately for a private repo),
  what happens next, scope, and the existing controls table and credential
  rule, with no em dashes.
- `LICENSE` is MIT, `Copyright (c) 2026 Izam Mohammed` (decision 2);
  `README.md` gets a licence line and its "change two things" step names
  the commented Dependabot block.

New dependencies: none. Permissions: none.

Security: no code, input or secret changes; the change adds update PRs and
a reporting policy, both of which reduce exposure.

## Where it lands

| Path | New or edit | Phase | What changes | Firm? |
| --- | --- | --- | --- | --- |
| `.github/dependabot.yml` | edit | 2 | ecosystems, cooldown, groups | fixed |
| `SECURITY.md` | edit | 3 | full policy | fixed |
| `LICENSE` | new | 4 | MIT text | fixed |
| `README.md` | edit | 2, 4 | Dependabot step, licence line | expected |

*Reused, not rewritten:*

- `SECURITY.md`: its controls table and "rotate first" rule stay.
- `/home/izam/fun/decisionsmith/.github/dependabot.yml` and `SECURITY.md`:
  the house shape both files follow.

## Declined

- Renovate: needs an app or a runner installed per repository (decision 1).
- Apache-2.0: lower total; its patent grant matters little for config
  files (decision 2).
- Keeping the `uv` block active: it fails on every run in the template.

## What the tests hold

`.github/dependabot.yml` parses as YAML, and every active block has a
manifest in the tree. `SECURITY.md` names no invented email, response time
or version.

## Phases

### Phase 1: topics

- [x] Repository topics set with `gh repo edit`: `dependabot`,
  `devsecops`, `github-actions`, `github-template`, `gitleaks`,
  `pre-commit`, `repository-template`, `security`.
- [x] Check: `gh repo view --json repositoryTopics` lists them.

### Phase 2: Dependabot

- [x] `.github/dependabot.yml` covers `github-actions` and `pre-commit`,
  weekly, 7 day cooldown, one group each, with a commented language block
  for copies; the failing `uv` block is gone.
- [x] `README.md` "change two things" step points at the commented block.
- [x] Check: the file parses with `python3 -c 'import yaml'`.

### Phase 3: security policy

- [x] `SECURITY.md` has supported versions, reporting for public and
  private repos, what happens next, scope, the controls table and the
  credential rule.
- [x] Check: `grep` finds no em dash and no email address in it.

### Phase 4: licence

- [x] `LICENSE` holds the MIT text with `Copyright (c) 2026 Izam Mohammed`,
  and `README.md` ends with `MIT, see [LICENSE](LICENSE).`.
- [x] Check: `LICENSE` matches the MIT text in `penpot-mcp` apart from the
  copyright line.

### Phase 5: open the PR

- [x] The branch pushed and its PR opened against `main` (or, if one is
  already open for this branch, bring the open PR's body up to date) with
  the title `chore: dependabot, security policy and licence`, a body
  naming this plan, what changed and why, the ecosystems covered and the
  checks run. The PR's URL printed as the evidence. Opened, never merged.
