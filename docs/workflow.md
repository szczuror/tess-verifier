# Workflow

How we work with this repository.

## Branches

- No pushing to `main` directly.
- new feature = new branch, created from the latest `main`. Keep branches small if possible.

```bash
git fetch origin
git switch -c <branch-name> origin/main
```

After implementing a feature, just push to your branch and create a PR.
```bash
git push -u origin <branch-name>
```

## Staying up to date

We rebase instead of merging `main` into a branch, so the history stays linear.

If your pull request is outdated, then you probably need to do this before merging:
```bash 
git fetch origin
git rebase origin/main
git push origin <branch-name> --force-with-lease
```

(Mind the slash in rebase)

## Pull requests

- When the branch is ready, open a PR into `main`. A draft PR is a good idea if you want feedback earlier.
- Short description that says what changed and why.
- Rebase onto the current `main` before merging.

## Review and merge

- A PR needs at least 1 approval from another team member.
- The reviewer checks that the change does what the description says, and asks questions rather than rewriting it.
- Before merging, the branch is up to date with `main` and CI passes.
- The author merges with **Rebase and merge**, then deletes the branch.

## CI

Once there is code, every PR runs the build and tests. A PR with failing checks is not merged until fixed.

## Milestone tags

At the end of each course stage we tag `main`:

| Tag | Stage |
|---|---|
| `vision-v1` | Project vision |
| `requirements-v1` | Requirements and glossary |
| `architecture-v1` | Architecture |
| `mvp-v1` | First working version |

```bash
git tag -a requirements-v1 -m "First version of requirements"
git push origin requirements-v1
```

## GitHub settings

- `main` is protected: PRs are required, with 1 approval and an up-to-date branch; once CI exists, its checks are required too.
- Only "Allow rebase merging" is enabled.
- "Automatically delete head branches" is on.
