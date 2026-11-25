# gitops

Shared GitHub Actions for avunu repositories: reusable workflows and composite actions that are not tied to any one product.

Each copied item was taken from python-devops-nix at `cb0774e`, from its `.github` directory. Two kinds of line changed. References to the items that moved here now name `Avunu/gitops`. And the two reusable workflows pin their actions by version tag instead of commit SHA, because `gh actions-lock` requires tags and rejects bare SHAs in the workflows it checks; `actions.lock` pins those tags to the same commits.

## Layout

| Path | Kind | What it does |
|---|---|---|
| `.github/workflows/dependabot-auto-merge.yml` | reusable workflow | Enables auto-merge on a Dependabot pull request that the caller's policy allows, once the required checks pass. |
| `.github/workflows/dependabot-update-branches.yml` | reusable workflow | Brings the open Dependabot pull requests that are behind their base branch up to date. |
| `.github/actions/check-report/` | composite action | Comments a workflow's job results on its pull request, with the details the failed jobs uploaded. |
| `.github/actions/nix-failures/` | composite action | Turns the log of a failed `nix build` or `nix flake check` into Markdown. |
| `.github/actions/port-offset/` | composite action | Picks a port offset for a CI job, so jobs that share a self-hosted host do not collide on ports. |
| `.github/actions/pre-commit-autofix/` | composite action | Runs the prek hooks over a pull request's branch and pushes the fixes they make. |
| `.github/actions/sticky-comment/` | composite action | Keeps one pull request comment per marker: creates it, or edits the one an earlier run left. |
| `.github/workflows/ci.yml` | this repository's CI | actionlint, zizmor (pedantic) and check-jsonschema over the workflows and the Dependabot configuration. |
| `.github/workflows/actions-lock.yml` | this repository's CI | Verifies `.github/workflows/actions.lock` with `gh actions-lock --verify`. |

`.github/dependabot.yml` keeps the GitHub Actions pins in this repository's workflows current: weekly, one group, a seven-day cooldown.

## Using them

Reusable workflows are called by full commit SHA, with the version in a trailing comment, so that Dependabot's github-actions updates move the pin. An Avunu caller may instead name the release tag itself (`@v1.0.0`), which the org's policy allows for an Avunu reusable workflow; the tag is not locked to a commit.

```yaml
jobs:
  auto-merge:
    uses: Avunu/gitops/.github/workflows/dependabot-auto-merge.yml@<full-commit-sha> # vX.Y.Z
```

Composite actions are called by version tag:

```yaml
steps:
  - uses: Avunu/gitops/.github/actions/sticky-comment@vX.Y.Z
```

The calling repository locks its workflows with [gh-actions-lock](https://github.com/github/gh-actions-lock). `gh actions-lock` writes `.github/workflows/actions.lock`, which pins every direct and transitive action to a commit SHA, and `gh actions-lock --verify` checks that lock in CI.

## Scope

An item belongs here when it is generic CI plumbing that any avunu repository can run. That means it:

- does not install, run or depend on the python-devops-nix standards engine (standards-core, standards_core, lib/checks, convention checks, standards profiles), and
- does not exist only to build, release, document or self-test python-devops-nix.

Standards, conventions, python-devops-nix's own gates, release, docs and self-test workflows, and the `standards-*` composite actions stay in python-devops-nix.

## python-devops-nix

python-devops-nix keeps its copies of the items above until its callers repoint to this repository. Until then the two sets are maintained separately, and a fix to one is not applied to the other automatically.

## License

MIT. See [LICENSE](LICENSE).
