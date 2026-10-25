# repo-template: what is left to build, in order

What is built is in `docs/built/BUILT-2026-10-22-1828.md`. The standards
every change passes are in `docs/standards.md`.

Not planned, because it does not apply: a site and a coverage floor for
the template itself (it holds configuration, no code, and its only user
reads the README); both are planned for the repositories made from it.

The language gate spans three repositories; their order is
`@.github/plans/master-language-gate.md`.

### Phase 1: repository hygiene

- [x] `plans/05-repo-hygiene.md` - Dependabot, security policy and licence.

### Phase 2: the template checks itself

- [ ] `plans/01-quality-gates.md` - `make check` with pre-commit, actionlint
  and zizmor, CI on every PR, the gate pin brought current.

### Phase 3: a new repository in any language starts green

- [ ] `plans/02-language-gate.md` - `starters/` and `scripts/init.sh` for
  seven languages, with lint, tests, a 90 % floor and the gate wired.

### Phase 4: README and site

- [ ] `plans/03-readme-banner.md` - the template's banner, and a skeleton
  README with a banner slot for new repositories.
- [ ] `plans/04-site.md` - a `site/` skeleton published by GitHub Pages
  for new repositories.
