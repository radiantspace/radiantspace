# Merge Dependabot PRs

Sanity-checks Dependabot pull requests, flags risky dependency changes, approves
safe updates, and enables auto-merge.

## Install

Install this skill for GitHub Copilot at user scope:

```sh
gh skill install radiantspace/radiantspace merge-dependabot-prs \
  --allow-hidden-dirs \
  --agent github-copilot \
  --scope user
```

Install every skill from this repository:

```sh
gh skill install radiantspace/radiantspace \
  --all \
  --allow-hidden-dirs \
  --agent github-copilot \
  --scope user
```
