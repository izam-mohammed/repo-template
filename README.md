# repo-template

Starting point for new repositories under
[`izam-mohammed`](https://github.com/izam-mohammed), pre-wired with the security
baseline so a new project is covered from its first commit.

## What you get

- `.github/workflows/security.yml` — calls the shared gate
- `.github/dependabot.yml` — dependency and action updates
- `.pre-commit-config.yaml` — gitleaks and lint hooks
- `.gitignore` — env and key patterns
- `SECURITY.md` — reporting policy

## After creating a repo from this template

1. Set `ecosystem` in `.github/workflows/security.yml` (`python` | `node` | `go` | `shell`).
2. Set `package-ecosystem` in `.github/dependabot.yml` to match, or delete that block.
3. For a public repo, turn on secret scanning and push protection in Settings → Code security.

The pre-commit hook installs itself on clone, because
`git config --global init.templateDir ~/.git-template` is set on this machine.
Nothing to run by hand.
