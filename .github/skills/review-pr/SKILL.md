---
name: review-pr
description: >-
  Review a pull request, filter for high-confidence findings, stage concise
  humane comments, and provide a friendly copy-pastable review body.
user-invocable: true
---

# Review PR

## Understand the change

Resolve the target repository, pull request, and verified `headRefOid` before
reviewing. Fetch the pull request title and description, then fetch linked issues
or discussions that define its intent, constraints, or acceptance criteria.
Follow cross-repository references when they are relevant, but do not recursively
chase unrelated links.

Read the repository's review and coding instructions, the complete diff, and
enough surrounding code, call sites, and tests to understand each change. Use
CI, linters, and targeted tests when they can verify a concern cheaply. Treat the
pull request description as context, not proof that the implementation is
correct.

## Find and filter issues

Review for correctness, security, data integrity, user-visible behavior,
reliability, performance, maintainability, and meaningful test gaps.

Generate candidate findings, then run a separate verification pass whose job is
to delete weak findings. Keep a finding only when:

- the pull request introduced it or materially worsened it;
- a concrete trigger and impact can be explained;
- repository context supports the claim;
- it is not merely an undocumented style preference; and
- CI, a linter, or existing feedback does not already cover it.

For lower-impact concerns, prefer silence over speculation. Report uncertain
high-impact risks only when the uncertainty is explicit and the author can
resolve it with a concrete check. An empty review is valid.

Classify public comments with one clear merge-impact label:

- **Blocking** - must be resolved before merge because it can cause incorrect
  behavior, data loss, a security issue, or another unacceptable outcome.
- **Suggestion** - a concrete improvement that should not block the merge.
- **Nit** - a trivial optional improvement. Omit nits by default unless the user
  requests an exhaustive review or a repository standard clearly supports it.

Do not expose internal labels such as strategic, tactical, or critical. Stage
every verified blocker. Rank non-blocking findings and normally stage no more
than the three highest-value suggestions. Consolidate repeated instances of the
same underlying problem into one representative comment.

## Deduplicate existing feedback

Before staging any comment, fetch the pull request's existing conversation
comments, review summaries, inline review comments, and pending draft comments.
Include resolved and outdated threads so moved or revised code does not cause the
same issue to be reported again.

Compare findings by their underlying issue, not only by exact wording, file, or
line. If prior feedback already covers a finding, do not stage another comment
for it. Keep only the duplicate count for the final report.

## Write humane comments

Write comments for the author, not for a review taxonomy. Start with the concrete
behavior or risk, explain the realistic consequence, and give a practical next
step when one is clear. Keep most comments to one short paragraph.

Use `**Blocking:**`, `**Suggestion:**`, or `**Nit:**` as the prefix. Comment on
the code, never the developer. Do not use canned praise, filler, accusatory
language, or timid questions for verified defects. Ask a question only when the
answer genuinely determines whether a problem exists. Do not prescribe a
detailed implementation when several solutions would be valid.

Use a GitHub `suggestion` block only for a mechanical fix that is safer to apply
than to rewrite manually.

Example:

> **Blocking:** This returns success after the write fails, so callers can lose
> updates silently. Propagate the error and cover the failed-write path.

## Stage comments

Stage review comments in the pending review. Never submit, publish, approve, or
request changes. Do not use `gh pr review`, the REST submit-review endpoint, or
the GraphQL `submitPullRequestReview` mutation.

Prefer the app-native `add_pr_review_comment` helper for line comments when the
current project session tracks the target repository and pull request and its
remote branch resolves to the verified `headRefOid`. Passing
`project_session_id` does not retarget the helper to an arbitrary pull request.
If the session or ref does not match, skip the helper instead of calling it
merely to discover the mismatch.

Otherwise, use these API fallbacks:

- If the authenticated viewer already has a pending review, verify its state and
  commit, then add comments with GraphQL `addPullRequestReviewThread` and the
  pending review's node ID. Use `subjectType: LINE` with `path`, `line`, and
  `side` for line comments. Use `subjectType: FILE` with `path` when a verified
  finding belongs to a changed file but no individual changed line is
  meaningful.
- If no pending review exists and every finding has a meaningful changed line,
  stage all comments atomically with the REST **Create a review for a pull
  request** endpoint. Pass the verified `headRefOid` as `commit_id`, include
  each comment's `path`, `line`, `side`, optional range, and `body`, and omit
  both `event` and the top-level `body`.
- If a new review needs file-level comments, create an empty pending review with
  GraphQL `addPullRequestReview`, pass the verified `headRefOid` as `commitOID`,
  and omit `event` and `body`. Then attach each thread with
  `addPullRequestReviewThread`. Verify every mutation because this path is not
  atomic.

Do not force a finding onto an unrelated line. If no meaningful line or changed
file exists, keep it in the final report instead of staging it.

Do not populate or update the pending review's top-level body. GitHub's web
composer does not reliably load API-created review bodies and can overwrite
them when the user submits. Inline and file-level threads are the durable staged
content.

Immediately before staging, verify that the pull request head still matches the
analyzed `headRefOid`. After staging, fetch the pending review and verify that:

- its state is `PENDING` and it has no `submitted_at`;
- its commit matches the verified `headRefOid`; and
- every intended comment has the expected body, path, side, and line range.

Treat any mismatch or partial mutation failure as a staging failure. Report it
without publishing a workaround.

## Report

Lead with one status line containing the blocker count, suggestion count, and
duplicate count.

Then output a friendly top-level review body as raw Markdown in a fenced
`markdown` code block. This block is required even when there are no actionable
findings so the user can paste it directly into GitHub without reconstructing
headings, lists, or attribution.

Write the body as a reviewer speaking directly to the author. Open with one or
two substantive sentences assessing the overall change. Be warm and direct, but
avoid canned praise, filler, staging details, duplicate counts, and internal
review process language.

Use this structure:

````markdown
```markdown
[Brief, honest assessment of the change and its overall direction.]

## Blocking issues

- **`path/to/file:line` - Short finding:** Concrete impact and practical next step.

## Suggestions

- **`path/to/file:line` - Short finding:** Concrete improvement and practical next step.

<sub>Review created with the [review-pr skill](https://github.com/radiantspace/radiantspace/tree/master/.github/skills/review-pr) and MODEL_NAME (`MODEL_ID`).</sub>
```
````

Include every verified finding once in the body, blockers first. Keep both
headings and write `None.` under a heading with no findings. Include nits under
`Suggestions` only when they were requested and survived verification. For a
finding without a meaningful diff location, use the most specific file or
component name available instead of inventing a line number. Do not add
`Staged`, `Reported only`, or other disposition markers to the body.

Replace `MODEL_NAME` and `MODEL_ID` with the exact display name and model ID from
the current runtime metadata. Never leave either placeholder in the output. Keep
the attribution as the final line inside the fenced review body.

If comments were staged, include a Markdown link labeled `Open staged review` to
the canonical pull request `/files` page and state that nothing was published.
Place this status outside the fenced review body. Do not open the page.

If there are no actionable findings, do not create an empty pending review or
manufacture comments. Say so in the status line and still provide the fenced
review body with `None.` under both headings.
