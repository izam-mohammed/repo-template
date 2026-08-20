# Security

## Reporting a vulnerability

Report privately through
[GitHub Security Advisories](../../security/advisories/new) rather than opening
a public issue. Expect an acknowledgement within a few days.

## What runs on this repository

| Layer | Control |
|---|---|
| Local | pre-commit hooks — gitleaks, private-key detection, linting |
| Push | GitHub secret scanning with push protection (public repos) |
| Pull request | Shared security gate — secret detection blocks the merge; dependency, SAST and workflow findings are reported |
| Dependencies | Dependabot alerts and weekly update PRs |

The gate is defined in
[`izam-mohammed/.github`](https://github.com/izam-mohammed/.github).

## If a credential is committed

Rotate it first — deleting the line does not help once it has been pushed, since
the value stays in history and in every clone. Then purge it from history with
`git filter-repo` and force-push.
