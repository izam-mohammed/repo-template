# a new repository in any of seven languages starts with lint, tests, a 90 % coverage floor and the security gate already passing

## The ask

Every repository should reach lint and format, tests, coverage of at least
90 % (target 95 %) and security checks, run by one local check command and
by CI on every pull request, reusing the shared gate (brief of 22/10/2026).
The template is where a new repository gets that for free. Today it gives a
Python-flavoured security baseline and two values to edit by hand; nothing
for lint, tests, coverage, standards or plans, and nothing for TypeScript,
Rust, Go, Swift, Kotlin or shell.

This is the `repo-template` half of a feature across three repositories;
the order is `@.github/plans/master-language-gate.md`. `.github` lands
`quality.yml` and the new ecosystems
(`@.github/plans/02-language-gate.md`, whose `## Contract` this plan
reads); `project-setup` documents the result.

Built so far: `docs/built/BUILT-2026-10-22-1828.md`.

## Constraints

- "Look at the shared reusable security gate ... and REUSE it rather than
  inventing a parallel one" (brief).
- House rule for scripts: POSIX `sh` with standard tools, no setup step
  (`~/.claude-fabulate/CLAUDE.md`).

## Alternatives

### Decision 1: how one template serves seven languages (one-way door)

Every new repository is shaped by this; repositories already made from the
template are not re-synced either way.

Searched 2026-10-22: "copier vs cookiecutter vs GitHub template repository
multi-language project template update"; Copier's own comparison page
(v9.7.0), the PyData London 2025 talk on Copier.

| Must | Baseline: edit two values by hand | `starters/<language>/` plus `scripts/init.sh` | One template repository per language | Copier template |
| --- | --- | --- | --- | --- |
| Works from `gh repo create --template` | yes | yes | yes | no (a separate `copier copy`) |
| No runtime to install before first use | yes | yes (POSIX `sh`) | yes | no (Python and Copier) |
| One place to change a shared file | yes | yes | no (seven copies) | yes |
| **Survives** | yes | **yes** | no | no |

| Criterion | Weight | Baseline: by hand | `starters/` plus `init.sh` |
| --- | ---: | ---: | ---: |
| Performance efficiency | 30 | 5 | 5 |
| Fit to the ask | 35 | 1 | 5 |
| Reliability | 20 | 2 | 4 |
| Reversibility | 15 | 5 | 4 |
| **Weighted total** | **100** | **3.00** | **4.65** |

- Fit: the baseline wires only security, and only for Python by default.
- Reliability: hand edits were already missed (the template's own
  Dependabot `uv` job fails on every run; `docs/built/BUILT-2026-10-22-1828.md`).
- Reversibility 4: `init.sh` deletes `starters/` after copying, so nothing
  ties the new repository back; the cost is no later re-sync, which Copier
  would give.

Sensitivity: the winner holds unless re-sync of existing repositories
becomes a must; then Copier comes back. Risk: a bad starter ships to every
new repository in that language; limited by the instantiation test in
Phase 1, run on every PR here.

**Chosen: `starters/<language>/` plus `scripts/init.sh <language>`.**
Revisit if keeping existing repositories in sync with the template becomes
a must.

### Decision 2: Python tools (two-way door)

Searched 2026-10-22: "ruff vs black flake8 speed benchmark", "pyright vs
mypy performance comparison large codebase; ty astral type checker";
Astral's Ruff docs, the pydevtools handbook comparison, a June 2026 laptop
benchmark, Dagster's ty report.

| Criterion | Weight | mypy | pyright | ty |
| --- | ---: | ---: | ---: | ---: |
| Performance efficiency | 40 | 4 | 3 | 5 |
| Fit to the ask (typing spec conformance) | 35 | 3 | 5 | 2 |
| Reliability | 25 | 4 | 5 | 2 |
| **Weighted total** | **100** | **3.65** | **4.20** | **3.20** |

- Conformance reported as about 58 % mypy, 98 % pyright, 53 % ty
  (pydevtools); speed reports conflict between pyright and mypy, ty
  fastest; ty judged too young for long-lived projects in one ADR.

Format and lint stay Ruff (already in the template; Astral reports 10 to
100 times Flake8 and Black), with the `S` rules in place of bandit as
`project-setup` already decides. Tests pytest with coverage.py through
pytest-cov, `--cov-fail-under=90`, LCOV out.

**Chosen: Ruff, pyright, pytest with pytest-cov.** Revisit ty when its
conformance passes 90 %.

### Decision 3: TypeScript tools (two-way door)

Searched 2026-10-22: "biome vs eslint prettier vs oxlint performance 2026
typescript"; Peter Bengtsson's oxlint and Biome benchmark, the 2026
comparisons on DEV and pkgpulse.

| Criterion | Weight | ESLint and Prettier (house pick in `project-setup`) | Biome | oxlint and Prettier |
| --- | ---: | ---: | ---: | ---: |
| Performance efficiency | 40 | 2 | 5 | 5 |
| Fit to the ask (lint, format, type-aware rules) | 35 | 5 | 4 | 3 |
| Compatibility (Next.js apps made with `--eslint`) | 25 | 5 | 3 | 3 |
| **Weighted total** | **100** | **3.80** | **4.15** | **3.80** |

- Biome reported 10 to 25 times faster than ESLint and formats too; oxlint
  as fast but lint only with most fixes manual.
- ESLint fit 5: the widest rule set, Next.js's own plugin.

Types `tsc --noEmit` strict; tests Vitest with V8 coverage, thresholds 90,
LCOV out.

Sensitivity: moving 10 points from efficiency to compatibility ties Biome
and ESLint, so this is fragile.

**Chosen: Biome, to be confirmed by the user over the house pick (ESLint
and Prettier), asked in this plan's PR.** Until answered, the Node starter
uses ESLint and Prettier as `project-setup` does today. Revisit on the
answer.

### Decision 4: Rust tools (two-way door)

Searched 2026-10-22: "cargo-llvm-cov vs cargo-tarpaulin accuracy speed",
"cargo-llvm-cov fail-under-lines option"; cargo-llvm-cov's README, the
Chorus migration PR `#619`, the Rust training book's coverage chapter.

| Criterion | cargo-llvm-cov | cargo-tarpaulin |
| --- | --- | --- |
| Performance efficiency | runs under nextest | own runner |
| Fit to the ask | `--fail-under-lines 90`, `--lcov` | thresholds, Linux only |
| Portability | Linux and macOS | Linux only |

rustfmt `--check` and Clippy `-D warnings` are the toolchain's own; tests
`cargo test`.

**Chosen: rustfmt, Clippy, cargo-llvm-cov.** Revisit never unless LLVM
coverage regresses.

### Decision 5: Go tools (two-way door)

Searched 2026-10-22: "golangci-lint vs staticcheck alone", "go-test-coverage
threshold"; golangci-lint's linter list, go-test-coverage's config docs.

| Criterion | golangci-lint (staticcheck, gosec, more) | staticcheck alone | go vet alone |
| --- | --- | --- | --- |
| Performance efficiency | parallel, cached | fast | fastest |
| Fit to the ask | lint and security rules in one run | lint only | the basics |

gofmt check; `go test -race -coverprofile`; govulncheck through the gate and
in `make check`; the floor read by the gate's parser and locally by
`go tool cover -func`.

**Chosen: gofmt, golangci-lint, `go test -race`, govulncheck.** Revisit if
golangci-lint's run time passes a minute on a starter-sized repository.

### Decision 6: Swift tools (two-way door)

Searched 2026-10-22: "swift test enable-code-coverage llvm-cov export
lcov"; the Swift forums threads on `llvm-cov export` and Linux; and
`mac-notch`'s `docs/standards.md`, which already chose swift-format over
SwiftLint and SwiftFormat on 18/10/2026.

| Criterion | swift-format (in the toolchain) | SwiftLint and SwiftFormat |
| --- | --- | --- |
| Performance efficiency | no install | two installs |
| Compatibility | what `mac-notch` runs | a second standard |

Tests Swift Testing under `swift test --enable-code-coverage`, exported to
LCOV with `llvm-cov export`; runs on a macOS runner.

**Chosen: swift-format and Swift Testing, as `mac-notch` does.** Revisit if
a rule only SwiftLint has is needed.

### Decision 7: Kotlin tools (two-way door)

Searched 2026-10-22: "ktlint vs detekt vs ktfmt", "Kover koverVerify
minBound line coverage Gradle"; the Kotlin docs on Kover, the ArcTouch
write-up on ktlint and detekt.

| Criterion | ktlint and detekt | ktfmt and detekt | ktlint alone |
| --- | --- | --- | --- |
| Fit to the ask | style and format, plus smells and complexity | format, plus smells | style only |
| Compatibility | Gradle plugins for both | Gradle plugin | Gradle plugin |

Tests JUnit 5 with `kotlin.test`; coverage Kover with a `minBound(90)` line
rule, XML out (JaCoCo format, which the gate reads).

**Chosen: ktlint, detekt, JUnit 5, Kover.** Revisit if ktlint's formatting
conflicts with Android Studio's.

### Decision 8: shell tools (two-way door)

Searched 2026-10-22: "bats-core vs shunit2 shell script testing
framework", "bash shell script code coverage kcov bashcov bats"; the 2026
Bats, ShellSpec and shUnit2 comparison, bashcov's README, a dotfiles
project's report of kcov v43 failing on bash 5.2.

| Criterion | bats-core and bashcov | shUnit2 and kcov | shUnit2, no coverage |
| --- | --- | --- | --- |
| Fit to the ask | tests and a Cobertura report | tests, coverage reported broken on bash 5.2 | tests only |
| Portability | bash 3.2 and Ruby for coverage | any `sh`; kcov a native build | any `sh` |

ShellCheck and shfmt for lint and format in every case.

**Chosen: ShellCheck, shfmt, bats-core and bashcov.** The only pair that
yields a coverage number today. A POSIX `sh` repository that bashcov cannot
trace sets the floor to `none` with that reason, as the gate allows.
Revisit if kcov works on current bash again.

### Decision 9: the local security check (two-way door)

Searched 2026-10-22: "osv-scanner supported lockfiles Cargo.lock
Package.resolved gradle.lockfile"; osv-scanner's supported-lockfiles page.

| Criterion | The gate's own tools locally (gitleaks, osv-scanner, govulncheck for Go) | Each language's auditor (pip-audit, npm audit, cargo audit) |
| --- | --- | --- |
| Compatibility | laptop and CI agree | the laptop says one thing, CI another |
| Fit to the ask | every lockfile, one tool | per language |

**Chosen: the gate's tools in `make check`, at the gate's pinned
versions.** Semgrep stays in CI only (needs Python and a rule download).
Revisit if the gate changes its tools.

## The decision

- `scripts/init.sh <language> [--no-site]` copies `starters/<language>/`
  over the root, writes the language into `check.yml`, `security.yml` and
  `dependabot.yml`, fills `docs/standards.md` for that language, then
  deletes `starters/` and itself (decision 1).
- Each starter carries a `Makefile` (`check`, `lint`, `format`, `test`,
  `test-cov`, `security`, names as `project-setup` step 8), the language's
  tool configs with the coverage floor at 90 (decisions 2 to 8), the
  pre-commit hooks for its formatter and linter, and the gate's local
  security tools (decision 9).
- Every new repository gets `.github/workflows/check.yml` calling
  `quality.yml` with its `language`, coverage file and format, and
  `security.yml` with its `ecosystem` and `fail-on: high`, both pinned to
  the `.github` tag.
- Every new repository also gets the planning skeleton: `PLAN.md` with an
  empty first phase, `plans/`, `docs/built/` and `docs/standards.md`
  filled for its language.
- The template's own `check.yml` switches to `quality.yml` with
  `coverage-floor: none` and the reason "configuration only".
- The template's `.github/dependabot.yml` drops the `uv` block; each
  starter carries its own (`uv`, `npm`, `cargo`, `gomod`, `swift`,
  `gradle`; none for shell).

New dependencies: none in the template; each new repository gets its
language's tools, all from the official registries, pinned in its lockfile.
No new permission or secret.

Standards: `docs/standards.md` (`make check` from
`plans/01-quality-gates.md`, plus `tests/run.sh` from this plan).

## Contract

Reads `@.github/plans/02-language-gate.md` `## Contract`: the `quality.yml`
inputs and the language-to-format table. The starters must produce exactly
those report paths and formats. This plan owns `scripts/init.sh`'s
arguments: `init.sh <python|node|rust|go|swift|kotlin|shell> [--no-site]`.

## Security

What is protected: every new repository's first commit and supply chain.

| Threat | Here | Handled by | Proved by |
| --- | --- | --- | --- |
| Tampering: a look-alike package in a starter | starter lockfiles and configs | every tool named from its official registry, versions pinned, checked against OSV.dev when chosen | `osv-scanner` in `tests/run.sh` over each instantiated starter |
| Tampering: code built from input | `init.sh <language>` | the argument matched against the seven names, refused otherwise; no `eval` | `tests/run.sh` case: `init.sh 'python; rm -rf /'` refused |
| Information disclosure | `.gitignore` carried into every repository | secrets patterns kept for every language | case: `init.sh` keeps `.env` ignored in each language |
| Elevation of privilege | `check.yml` in new repositories | `permissions: {}` top, minimal per job, no `secrets: inherit` | zizmor clean over every instantiated starter |

Secrets: none. Install scripts: the Node starter installs with
`--ignore-scripts` as `project-setup` step 4 says. Checklist: SLSA build L1
and the OSV musts in the skill's supply-chain section.

## Where it lands

| Path | New or edit | Phase | What changes | Firm? |
| --- | --- | --- | --- | --- |
| `scripts/init.sh` | new | 1 | specialises the template | fixed |
| `starters/python/` | new | 1 | Python starter | expected |
| `starters/node/`, `starters/rust/`, `starters/go/` | new | 2 | starters | expected |
| `starters/swift/`, `starters/kotlin/`, `starters/shell/` | new | 3 | starters | expected |
| `starters/common/` | new | 1 | `PLAN.md`, `plans/`, `docs/built/`, `docs/standards.md` skeleton | expected |
| `tests/run.sh` | new | 1 | instantiates each starter in a temp directory and runs its check | expected |
| `.github/workflows/check.yml` | new in plan 01 | 1 | calls `quality.yml`, plus a matrix job running `tests/run.sh` per language | expected |
| `.github/dependabot.yml` | edit | 1 | `uv` block removed | reuse |
| `README.md` | edit | 4 | "usin' it" becomes `init.sh <language>` | reuse |

*Reused, not rewritten:*

- `@project-setup/templates/Makefile.python` and `Makefile.node`: target
  names and commands, raised to the 90 floor; `project-setup` updates its
  own copies in its plan of the same name.
- The `.pre-commit-config.yaml` gitleaks and hygiene hooks: kept in every
  starter; only language hooks change.
- `mac-notch`'s `docs/standards.md` for the Swift rows.
- The skill's `TEMPLATE-STANDARDS.md` as the shape of each starter's
  `docs/standards.md`.

## Open decisions

- TypeScript lint: Biome won (decision 3) over the house pick: default,
  ESLint and Prettier until the user answers in the PR.

## Declined

- Hand edits: miss the ask, already proved error-prone (decision 1).
- One template per language: seven copies of every shared file
  (decision 1).
- Copier: not usable from `gh repo create --template`, needs Python first
  (decision 1).
- mypy and ty: lower conformance, ty too young (decision 2).
- oxlint: lint only (decision 3).
- cargo-tarpaulin: Linux only (decision 4).
- staticcheck or go vet alone: no security rules in the same run
  (decision 5).
- SwiftLint and SwiftFormat: a second standard beside `mac-notch`
  (decision 6).
- ktfmt, ktlint alone: less fit (decision 7).
- shUnit2 with kcov or without coverage: no working coverage number
  (decision 8).
- Per-language auditors: the laptop and CI would disagree (decision 9).

## What the tests hold

`tests/run.sh` creates a temp copy of the template per language, runs
`init.sh`, adds the starter's sample function and its test, and runs
`make check`: it must pass at or over 90 %, fail when the sample test is
deleted (coverage under the floor), and leave no `starters/` behind. CI
runs it as a matrix, Swift on macOS.

## Repository knowledge

Read root `KNOWLEDGE.md` when present before each phase. Follow the global
and repository instructions for recording useful development findings.

## Phases

### Phase 1: the rough cut, a Python repository from the template (needs you: try it)

- [ ] `scripts/init.sh python` copies `starters/python/` and
  `starters/common/` into place, sets the language in the workflows and
  Dependabot, and removes `starters/` (expected in `scripts/init.sh`),
  pinned by `tests/run.sh`.
- [ ] The Python starter's `make check` runs Ruff, pyright, pytest with a
  90 % floor writing `coverage.lcov`, gitleaks and osv-scanner (expected in
  `starters/python/Makefile` and `starters/python/pyproject.toml`).
- [ ] `.github/workflows/check.yml` calls `quality.yml` (needs
  `@.github/plans/02-language-gate.md` phase 1) and runs `tests/run.sh` for
  Python; `.github/dependabot.yml` loses its failing `uv` block.
- [ ] Check: `make check` passes, and `sh tests/run.sh python` prints PASS.
- [ ] You try it: create a scratch repository from this branch's template,
  run `sh scripts/init.sh python`, push a PR and say whether the check and
  security comments are what you want to see.

### Phase 2: Node, Rust and Go

- [ ] Starters for `node`, `rust` and `go` per decisions 3 to 5 (expected
  in `starters/node/`, `starters/rust/`, `starters/go/`), each passing its
  own `make check` in `tests/run.sh`.
- [ ] Check: `make check` passes, and the CI matrix is green for all four
  languages.

### Phase 3: Swift, Kotlin and shell

- [ ] Starters for `swift` (macOS runner), `kotlin` and `shell` per
  decisions 6 to 8 (expected in `starters/swift/`, `starters/kotlin/`,
  `starters/shell/`), using the gate's new ecosystems (needs
  `@.github/plans/02-language-gate.md` phase 3).
- [ ] Check: `make check` passes, and the CI matrix is green for all seven.

### Phase 4: documented

- [ ] `README.md` explains `init.sh <language>` in the house voice and
  drops the "change exactly two things" step; `SECURITY.md` names the new
  layers.
- [ ] Check: `make check` passes.

### Phase 5: open the PR

- [ ] The branch pushed and its PR opened against the branch it was cut
  from (or, if one is already open for this branch, its body brought up to
  date) with a Conventional Commit title for the whole change, a body
  naming this plan, what changed and why, the open TypeScript question, and
  the checks run with their result. The PR's URL printed as the evidence.
  Opened, never merged.
