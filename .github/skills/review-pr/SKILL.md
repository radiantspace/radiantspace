---
name: review-pr
description: Review a pull request, triage findings as strategic, tactical, or nits, and stage friendly review comments without publishing them.
---

# Review PR

## Gather context

Before analyzing the diff, fetch the pull request title and description. Identify
every referenced or linked issue, including cross-repository references, and fetch
each issue's title and description.

Use this context to understand the intended behavior, constraints, and acceptance
criteria. Then review the complete pull request diff and enough surrounding code
to understand the impact of each change. Do not treat the pull request or issue
description as proof that the implementation is correct.

## Triage

Classify every actionable finding:

- **Strategic** - architecture, product direction, security, data integrity, or a
  correctness issue that may require changing the approach.
- **Tactical** - a localized implementation, reliability, performance,
  maintainability, or test coverage issue.
- **Nit** - an optional, low-impact improvement to naming, readability,
  consistency, or style.

Verify each finding against the repository context. Do not raise speculative,
duplicate, or pre-existing issues unless the pull request makes them materially
worse.

## Deduplicate existing feedback

Before staging any comment, fetch the pull request's existing conversation
comments, review summaries, inline review comments, and pending draft comments.
Include resolved and outdated threads so moved or revised code does not cause the
same issue to be reported again.

Compare findings by their underlying issue, not only by exact wording, file, or
line. If prior feedback already covers a finding, do not stage another comment
for it. Keep track of skipped duplicates for the final report.

## Stage comments

Stage review comments in the pending review. Never submit, publish, approve, or
request changes.

Write each comment as a friendly, direct suggestion or question. Prefix it with
its triage category, explain the concrete impact, and propose a practical next
step when useful.

Attach comments to the most relevant changed lines whenever the diff provides a
meaningful location. If no changed line is suitable, keep the finding in the
triage summary instead of forcing it onto an unrelated line.

Example:

> **Tactical:** Could we handle the failed write here instead of continuing?
> Otherwise the response reports success even though the update was lost.

## Report

Summarize the staged findings by category, including the file and line when
available, and note how many duplicates were skipped. Clearly state that the
review remains staged and unpublished. If there are no actionable findings, say
so and do not manufacture comments.
