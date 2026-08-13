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

Also assign each finding a merge impact:

- **Blocker** - must be fixed before merge because it can cause incorrect
  behavior, data loss, a security issue, or another unacceptable outcome.
- **Critical** - has substantial user or operational impact and should be fixed
  before merge, but does not invalidate the overall approach.
- **Non-blocking** - useful follow-up that does not need to hold the merge.

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

After staging the inline comments, prepopulate the pending review's top-level
review body with a friendly, helpful high-level summary. This is part of the
staged review, not a separate conversation comment. Never submit or publish it.
List every blocker and critical finding with its location, concrete impact, and
recommended next step. Mention that non-blocking suggestions are staged inline.
If there are no blockers or critical findings, say that explicitly instead of
leaving the body empty.

Use this structure:

```markdown
Thanks for the work here. The overall direction is [brief assessment].

## Blockers
- **`path/to/file:line` - Finding:** Impact and practical next step.

## Critical findings
- **`path/to/file:line` - Finding:** Impact and practical next step.

Non-blocking suggestions are staged inline.

<sub>Review created with the [review-pr skill](https://github.com/radiantspace/radiantspace/tree/master/.github/skills/review-pr) and MODEL_NAME (`MODEL_ID`).</sub>
```

Keep both headings and write `None.` under a heading with no findings.
Replace `MODEL_NAME` and `MODEL_ID` with the exact display name and model ID from
the current runtime metadata. Never leave either placeholder in the staged
review. If the model changed during the workflow, identify the model that
performed the analysis and staged the review. Keep the attribution as the final
line of the top-level review body.

Example:

> **Tactical:** Could we handle the failed write here instead of continuing?
> Otherwise the response reports success even though the update was lost.

## Report

Report every verified finding in one self-contained Markdown table so the reader
does not need to open or scroll through the inline comments to understand the
analysis. Sort blockers first, then critical findings, then non-blocking
findings. Use these columns:

| Impact | Category | Location | Full analysis | Recommended next step | Disposition |
| --- | --- | --- | --- | --- | --- |

Repeat the complete substance of each finding in the table, including its
concrete impact and reasoning. Do not replace the analysis with a short title or
refer the reader to an inline comment. The disposition must say whether the
finding was staged inline, kept in the summary because no suitable changed line
exists, or skipped as duplicate existing feedback. Include duplicate findings
in the table and report the total number skipped.

After the table, include a Markdown link labeled `Open staged review` to the pull
request's Files changed review page, using the canonical pull request URL with
`/files`. Clearly state that the review and its prepopulated summary remain
staged and unpublished.

If there are no actionable findings, say so, report the duplicate count, include
the staged-review link, and do not manufacture comments.
