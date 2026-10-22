# every new repository can publish a one-page site from site/ by merging to main

## The ask

Every repository should eventually have a `site/` folder holding a
presentable one-page site for users, like `mac-notch/site/index.html`, and
a known way it is published (brief of 22/10/2026). The template is where
the convention lives so each repository does not reinvent it.

What the reference turned out to be: `mac-notch/site/index.html` is one
self-contained HTML file (7,769 bytes on 22/10/2026), with inline CSS and
an inline SVG favicon, no scripts and no fetched fonts (commit `9e588fc`
says so). How it is published could not be found: `mac-notch` has no Pages
site (`gh api repos/izam-mohammed/mac-notch/pages` returns 404), no
deployments, no homepage set and no workflow touching `site/`. So the
publishing half is a new decision, made here once for every repository.

Not applicable: a site for `repo-template` itself. It is a template whose
only user is the person creating a repository, and that person reads the
README on GitHub. `scripts/init.sh` takes `--no-site` for infrastructure
repositories that will not have one either.

Built so far: `docs/built/BUILT-2026-10-22-1828.md`.

## Alternatives

### Decision 1: where a repository's site is published (one-way door)

Every new repository inherits this, and links to the site spread from
READMEs and app stores, so moving later costs broken links.

Searched 2026-10-22: "GitHub Pages deploy static site from subfolder
actions/deploy-pages vs Cloudflare Pages vs Vercel static hosting
comparison", "GitHub Pages private repository availability GitHub Free Pro
plan"; GitHub's Pages limits page, the `actions/upload-pages-artifact`
README, GitHub's plans page, a 2026 static hosting comparison.

| Must | GitHub Pages, deployed by an Actions workflow from `site/` | Vercel (the house host in `project-setup`) | Cloudflare Pages |
| --- | --- | --- | --- |
| No secret in the template or the new repository | yes (the workflow's own token with `pages: write`) | no (a Vercel deploy token for a workflow, or a dashboard link per project) | no (API token, or a dashboard link per project) |
| Works the moment the repository exists | yes, once Pages is set to "GitHub Actions" | no | no |
| **Survives** | **yes** | no | no |

Only one option survives on the musts. What it costs, so the choice is not
blind:

- Limits: published site up to 1 GB, soft 100 GB a month bandwidth, the 10
  builds an hour limit not applying to Actions deploys, 10 minute deploy
  timeout (GitHub's Pages limits page).
- Private repositories: GitHub's plans page lists Pages for private
  repositories on paid plans only; third-party sources conflict. A private
  repository either stays without a site, or its site moves to Vercel
  under `project-setup`'s rules with the token in Infisical.

Risk: Pages is turned off or the account loses Actions minutes (Actions runs
fail account-wide since 21/10/2026 with "recent account payments have failed
or your spending limit needs to be increased"); the site keeps serving its
last deploy, and the workflow is a small file to swap.

**Chosen: GitHub Pages, deployed from `site/` by an Actions workflow.**
Revisit if a site needs a backend, or most new repositories are private.

### Decision 2: what a site is made of (two-way door)

Searched 2026-10-22: the same comparison; and `mac-notch/site/index.html`
itself.

| Criterion | One self-contained `index.html` (as `mac-notch`) | A static site generator | A framework app |
| --- | --- | --- | --- |
| Performance efficiency | one request, no script | a build step, small output | a bundle, hydration |
| Security | no script, no third-party request | depends on its plugins | a dependency tree |
| Fit to the ask | one page for users | many pages | an app |

**Chosen: one self-contained `index.html`.** Inline CSS, inline SVG
favicon, light and dark through `prefers-color-scheme`, no script, no
external font or image, a 16 px side gutter at phone width. Revisit if a
repository needs more than one page.

## The decision

- `starters/common/site/index.html` is the skeleton: the repository's
  name, its one-line pitch, the banner or screenshot, a "get it" button and
  a short "what it does" list, built the way `mac-notch`'s page is
  (decision 2).
- `starters/common/.github/workflows/pages.yml` uploads `site/` with
  `actions/upload-pages-artifact` and deploys with `actions/deploy-pages` on
  push to `main` when `site/**` changes, and by hand (decision 1).
- `scripts/init.sh --no-site` removes both for infrastructure repositories.
- `tests/run.sh` checks every instantiated site: no `<script`, no `http`
  URL in `src` or `href` attributes except links, a `<title>`, a
  viewport meta.

New dependencies: `actions/upload-pages-artifact`, `actions/deploy-pages`
and `actions/configure-pages`, pinned by SHA. New permission in new
repositories: `pages: write` and `id-token: write` on the deploy job only,
which `deploy-pages` needs. No secret.

Standards: `docs/standards.md` (`make check`).

## Security

What is protected: the repository's Pages deployment and the visitor.

| Threat | Here | Handled by | Proved by |
| --- | --- | --- | --- |
| Elevation of privilege | `pages.yml` token | `permissions: {}` at top; `contents: read` on build, `pages: write` and `id-token: write` only on deploy | zizmor clean over every instantiated starter |
| Tampering: a third-party script on the page | `site/index.html` | no script and no external resource, by rule | `tests/run.sh` site case |
| Information disclosure | publishing the wrong folder | only `site/` is uploaded, never the root | `tests/run.sh` case: the upload `path` is `site` |
| Repudiation | who deployed | Pages deployments listed per commit in the `github-pages` environment | n/a, GitHub's own record |

Data: none collected; no analytics. Checklist: OWASP Top 10 (2021 edition,
searched again when Phase 1 lands) for a static page: the relevant item is
A05 security misconfiguration, held by the no-script rule.

## Where it lands

| Path | New or edit | Phase | What changes | Firm? |
| --- | --- | --- | --- | --- |
| `starters/common/site/index.html` | new | 1 | the skeleton page | expected |
| `starters/common/.github/workflows/pages.yml` | new | 1 | deploy `site/` | expected |
| `scripts/init.sh` | new in plan 02 | 1 | `--no-site` | expected |
| `tests/run.sh` | new in plan 02 | 1 | site cases | expected |
| `README.md` | edit | 2 | one line on the site convention | reuse |

*Reused, not rewritten:*

- `mac-notch/site/index.html`: the structure and rules (inline everything,
  no script), not its look or words.
- `scripts/init.sh` and `tests/run.sh` from `plans/02-language-gate.md`.

## Declined

- Vercel and Cloudflare Pages: each needs a token or a per-project
  dashboard link, so the template cannot publish on its own (decision 1).
  Vercel stays the answer for a private repository's site.
- A static site generator or framework: a build and dependencies for one
  page (decision 2).

## What the tests hold

`tests/run.sh` instantiates a starter with and without `--no-site`: with it,
neither `site/` nor `pages.yml` remain; without it, the page passes the
site case and `pages.yml` uploads only `site/`.

## Repository knowledge

Read root `KNOWLEDGE.md` when present before each phase. Follow the global
and repository instructions for recording useful development findings.

## Phases

### Phase 1: the rough cut, a published page from a fresh repository (needs you: try it)

- [ ] `starters/common/site/index.html` and
  `starters/common/.github/workflows/pages.yml` land per the decision, and
  `scripts/init.sh` honours `--no-site`, pinned by the site cases in
  `tests/run.sh`.
- [ ] Check: `make check` passes, and `sh tests/run.sh python` prints PASS
  for the site cases.
- [ ] You try it: in a public scratch repository made from this branch, set
  Pages to "GitHub Actions", push a change under `site/` and open the
  Pages URL on a phone and a laptop; say whether it is the page you want
  every project to start from.

### Phase 2: documented

- [ ] `README.md` gains one line in the house voice on `site/`, Pages, and
  `--no-site`.
- [ ] Check: `make check` passes.

### Phase 3: open the PR

- [ ] The branch pushed and its PR opened against the branch it was cut
  from (or, if one is already open for this branch, its body brought up to
  date) with a Conventional Commit title for the whole change, a body
  naming this plan, what changed and why, and the checks run with their
  result. The PR's URL printed as the evidence. Opened, never merged.
