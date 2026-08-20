# repo-template

Every new repo starts here ~ so future me doesn't have to remember any of this.

Basically the boring safety stuff, pre-wired. You get to skip straight to the part where you
break things.

## what's in the box

| file | what it actually does |
|---|---|
| `.github/workflows/security.yml` | yells at you before GitHub does |
| `.github/dependabot.yml` | opens PRs you'll ignore for three weeks, then merge in a panic |
| `.pre-commit-config.yaml` | catches the AWS key you were *definitely* not about to commit |
| `.gitignore` | `.env` lives here now. no exceptions. we've been through this |
| `SECURITY.md` | the responsible-adult file |

## usin' it

```bash
gh repo create izam-mohammed/<name> --private --template izam-mohammed/repo-template --clone
```

then change exactly two things, or the scanners go lookin' for the wrong stuff:

1. `ecosystem` in `.github/workflows/security.yml` → `python` | `node` | `go` | `shell`
2. `package-ecosystem` in `.github/dependabot.yml` → `uv` | `npm` | `pip` | `gomod`

that's it. go break somethin'.

## the gate

| stage | tool | mood |
|---|---|---|
| secrets | gitleaks | **blocks the merge.** non-negotiable |
| dependencies | osv-scanner, govulncheck | disappointed, but lets you through |
| code | semgrep | mildly concerned |
| workflows | zizmor | surprisingly good at its job |

Only the first one stops you. The rest leave a comment on the PR and let you live with yourself.
Tighten one by droppin' its `|| true` in [`izam-mohammed/.github`](https://github.com/izam-mohammed/.github)
once the backlog stops bein' embarrassing.

## the one rule

**rotate first, delete after.**

Deletin' the line does nothin' ~ it's still sittin' in the history, in every clone anyone ever
made, and in whatever mirror some bot scraped at 3am. Rotate the credential, *then* clean up the
code. In that order. Always. Ask me how I know.

## when a hook blocks you

good. that was the whole point.

If it's genuinely a false positive, fix the pattern ~ don't `--no-verify` your way past it. That's
the exact move that puts the key on the internet and turns a Tuesday into a very polite email from
someone at AWS.

---

<sub>pairs well with [`project-setup`](https://github.com/izam-mohammed/project-setup), which
explains the rest of the decisions you're about to re-make from scratch anyway.</sub>
