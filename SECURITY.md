# Security policy

## Supported versions

Only the latest commit on `main` gets security fixes. There are no
releases yet; once there are, list the supported ones here.

## Reporting a vulnerability

Please do not open a public issue, pull request or discussion for a
security problem.

- **Public repository:** report it privately through GitHub:
  [Report a vulnerability](../../security/advisories/new) (the "Security"
  tab, then "Report a vulnerability").
- **Private repository:** only collaborators can see it. Report the
  problem to the owner, [@izam-mohammed](https://github.com/izam-mohammed),
  privately on GitHub, never in an issue or pull request.

Helpful details:

- the commit you looked at
- what an attacker could do, and what they need first
- the smallest steps that show it
- any fix you have in mind

Please leave out real credentials and real personal data.

## What happens next

This is maintained by one person, so there are no guaranteed response
times. The aim is to:

- acknowledge the report
- agree on whether it is a vulnerability and how serious it is
- fix it privately, then publish a GitHub security advisory that credits
  you (unless you would rather not be named)

Please allow a reasonable chance to ship a fix before sharing details
publicly.

## Scope

In scope: the code, configuration and GitHub Actions workflows in this
repository. Problems in the shared security gate itself belong to
[`izam-mohammed/.github`](https://github.com/izam-mohammed/.github).

## What runs on this repository

| Layer | Control |
|---|---|
| Local | pre-commit hooks: gitleaks, private-key detection, linting |
| Push | GitHub secret scanning with push protection (public repos) |
| Pull request | Shared security gate: secret detection blocks the merge; dependency, SAST and workflow findings are reported |
| Dependencies | Dependabot alerts and weekly update PRs |

The gate is defined in
[`izam-mohammed/.github`](https://github.com/izam-mohammed/.github).

## If a credential is committed

Rotate it first. Deleting the line does not help once it has been pushed,
since the value stays in history and in every clone. Then purge it from
history with `git filter-repo` and force-push.
