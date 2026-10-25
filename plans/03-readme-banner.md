# the template's README opens with its own banner, and every new repository starts with a minimal README that has a slot for one

## The ask

Every repository should have a proper minimal README with a banner (a light
and a dark SVG, per the `github-banner` skill) or a UI screenshot near the
top (brief of 22/10/2026). Two halves here: the template's own README has no
banner, and a repository made from the template inherits the template's
README, which describes the template, not the new project.

Built so far: `docs/built/BUILT-2026-10-22-1828.md`.

## Alternatives

### Decision 1: banner or screenshot for the template's own README (two-way door)

The template has no screen; what a person meets is the file list and the
README.

Searched 2026-10-22: "GitHub README picture prefers-color-scheme dark light
image"; the GitHub Blog post on images for dark and light mode, GitHub's
writing docs.

| Criterion | Baseline: no image | Banner, two SVGs | Screenshot of the file tree |
| --- | --- | --- | --- |
| Performance efficiency | nothing | two small text SVGs | a PNG per theme |
| Fit to the ask | misses it | says what it does | shows files, not purpose |

**Chosen: banner, two SVGs.** Revisit never.

### Decision 2: what a new repository's README starts as (two-way door)

Searched 2026-10-22: "copier vs cookiecutter vs GitHub template repository
multi-language project template update" (how templates hand over a README);
Copier's comparison page.

| Criterion | Baseline: the template's README, copied | A skeleton README written by `init.sh` | No README, written by hand |
| --- | --- | --- | --- |
| Fit to the ask | describes the wrong project | minimal, with the `<picture>` slot and the standard sections | often never written |
| Reliability | misleading from the first commit | `tests/run.sh` checks it is there and names the repository | n/a |

**Chosen: a skeleton README written by `init.sh`.** It names the
repository, opens with the `<picture>` block pointing at
`assets/banner-light.svg` and `assets/banner-dark.svg` (left empty with a
comment to run the `github-banner` skill or swap in a UI screenshot), then
"what it does", "get it", "build it yourself" with `make check`, and a link
to `PLAN.md`. Revisit if new repositories keep deleting a section.

## The decision

- The template's own README gets a banner pair from the `github-banner`
  skill, keeping its voice (decision 1).
- `starters/common/README.md` is the skeleton `init.sh` moves into place,
  replacing the template's README in the new repository (decision 2).
- No banner is generated for a new repository: a banner is a fresh concept
  per project (the skill's rule 4), so the slot waits for one.

No new dependency, permission or secret.

Standards: `docs/standards.md` (`make check`).

Security: no new input, data, secret, dependency or permission; SVGs hold
no script or external resource, which the skill's build refuses.

## Where it lands

| Path | New or edit | Phase | What changes | Firm? |
| --- | --- | --- | --- | --- |
| `assets/banner-light.svg` | new | 1 | template's light banner | fixed (skill rule) |
| `assets/banner-dark.svg` | new | 1 | template's dark banner | fixed (skill rule) |
| `README.md` | edit | 1 | `<picture>` at the top | reuse |
| `starters/common/README.md` | new | 2 | the skeleton | expected |
| `scripts/init.sh` | new in plan 02 | 2 | moves the skeleton, fills the name | expected |

*Reused, not rewritten:*

- The `github-banner` skill's build and preview scripts, run outside the
  repository.
- `mac-notch`'s `README.md` shape (one-line pitch, image, what it does, get
  it, build it yourself) as the skeleton's section order.
- `scripts/init.sh` from `plans/02-language-gate.md`: one more file moved.

## Declined

- No image, and a file-tree screenshot: miss the purpose (decision 1).
- Copying the template's README, or none: wrong or missing from day one
  (decision 2).

## What the tests hold

The skill's build exits 0 for the template's banner. `tests/run.sh` checks
that every instantiated starter has a `README.md` naming the repository,
holding the `<picture>` block, and not mentioning `repo-template`'s own
setup steps.

## Repository knowledge

Read root `KNOWLEDGE.md` when present before each phase. Follow the global
and repository instructions for recording useful development findings.

## Phases

### Phase 1: the rough cut, the template's banner (needs you: try it)

- [x] A fresh concept, checked against the skill's `concepts-used.md`, built
  into `assets/banner-light.svg` and `assets/banner-dark.svg` with the
  skill's build exiting 0.
- [x] `README.md` opens with the `<picture>` block; the rest keeps its
  voice.
- [ ] Check: `make check` passes, and the skill's preview shows both themes
  clean.
- [ ] You try it: open the branch's README on GitHub in both themes and say
  whether the joke lands.

### Phase 2: the skeleton README for new repositories

- [ ] `starters/common/README.md` holds the skeleton of decision 2, and
  `scripts/init.sh` moves it into place with the repository's name filled
  in, pinned by the README case in `tests/run.sh`.
- [x] The concept is logged in the skill's `concepts-used.md`.
- [ ] Check: `make check` passes, and `sh tests/run.sh python` shows the
  README case PASS.

### Phase 3: open the PR

- [x] The branch pushed and its PR opened against the branch it was cut
  from (or, if one is already open for this branch, its body brought up to
  date) with a Conventional Commit title for the whole change, a body
  naming this plan, what changed and why, and the checks run with their
  result. The PR's URL printed as the evidence. Opened, never merged.
