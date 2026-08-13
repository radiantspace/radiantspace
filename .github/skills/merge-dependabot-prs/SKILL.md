---
name: merge-dependabot-prs
description: Sanity-check Dependabot pull requests, flag risky dependency changes before approval, approve safe updates, and enable auto-merge.
---

# Merge Dependabot PRs

Process each pull request independently. Never approve a pull request only because
it looks automated or was opened by a bot.

## Verify the pull request

Confirm the pull request is open, is not a draft, and was created by the genuine
`dependabot[bot]` GitHub App identity. Do not trust the title, description, branch
name, or commit author as proof of identity.

Fetch the pull request title, description, changed files, complete diff, checks,
reviews, mergeability, and dependency version change before taking action.

## Sanity check

Require all of the following:

- Every required CI check has completed successfully. Pending, cancelled, timed
  out, or failing required checks block approval.
- GitHub reports the pull request as mergeable with no conflicts. If mergeability
  is unknown, refresh it instead of assuming it is safe.
- The diff is limited to the expected dependency manifests, lockfiles, and
  generated dependency metadata.
- Manifest and lockfile changes agree with the dependency and version described
  by the pull request.
- Lockfile changes do not introduce unexplained registry, source, checksum,
  package, or transitive dependency changes.

Do not bypass branch protection, dismiss reviews, ignore failed checks, or merge
the pull request directly.

## Assess risk

Review the actual changes and available release notes before approval. Treat any
of the following as risky:

- major version updates or documented breaking changes
- runtime, framework, compiler, build, deployment, authentication, cryptography,
  networking, persistence, or serialization dependency changes
- new package registries, Git sources, binary artifacts, native extensions, or
  install-time scripts
- unexpectedly large transitive dependency or lockfile churn
- dropped platform or runtime support, required migrations, or configuration
  changes
- source code changes or other files unrelated to the dependency update
- missing, inconclusive, or suspicious validation

This list is not exhaustive. A minor or patch update can still be risky.

If a change is risky or uncertain, stop before approval and clearly report the
specific risk for human review. Do not approve or enable auto-merge.

## Approve

After every sanity and risk check passes, approve the pull request. Keep the
approval message concise and mention that CI, mergeability, and dependency scope
were checked. Do not submit a duplicate approval if the current reviewer has
already approved the same revision.

## Enable auto-merge

Enable auto-merge using the repository's configured merge method. Do not merge
immediately or bypass repository protections. Confirm that auto-merge is enabled;
if GitHub or repository policy rejects it, report the blocker without attempting
a workaround.

## Report

For every processed pull request, report the dependency update, risk assessment,
CI and mergeability status, approval result, and auto-merge state. Put blocked or
risky pull requests first.
