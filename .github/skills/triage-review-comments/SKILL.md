---
name: triage-review-comments
description: Fetch pull request review comments, verify and triage the feedback, and report what needs attention without taking action.
---

# Triage Review Comments

## Gather context

Identify the pull request from the current session or the user's explicit
reference. Fetch its title, description, complete diff, and every referenced or
linked issue, including cross-repository issues.

Fetch all unresolved review threads and their full comment history, including
replies, outdated threads, and pending review comments. Also fetch review
summaries and conversation comments that contain review feedback.

Fetch resolved review threads only as context for prior decisions, duplicate
detection, and changes already made. Never verify, triage, or report resolved
threads as work items, and exclude them from all triage counts.

Do not rely on a comment's current file or line alone. Inspect enough surrounding
code and pull request history to understand whether later changes already
addressed the feedback.

## Verify feedback

Evaluate each unresolved review thread against the current code, tests, pull
request intent, and repository conventions. Treat reviewer feedback as a claim
to verify, not an instruction to follow automatically.

Group unresolved comments that discuss the same underlying issue so replies,
repeated comments, and moved code are triaged once. Preserve links to every
grouped unresolved comment in the report.

## Triage

Assign each unique unresolved thread a disposition:

- **Action required** - the feedback is valid and the current code still needs a
  change.
- **Needs clarification** - the concern or expected outcome is ambiguous, or a
  product or design decision is required.
- **Already addressed** - the current code resolves the concern, whether before
  or after the comment was written.
- **Duplicate or obsolete** - another thread covers the same issue, or the
  referenced code no longer exists.
- **No action** - the feedback is incorrect, outside the pull request's scope, or
  not worth changing. Give a concrete reason.

For **Action required** and **Needs clarification**, also classify impact:

- **Strategic** - architecture, product direction, security, data integrity, or
  correctness that may require changing the approach.
- **Tactical** - localized implementation, reliability, performance,
  maintainability, or test coverage.
- **Nit** - optional, low-impact naming, readability, consistency, or style.

State confidence as high, medium, or low. Do not hide uncertainty or manufacture
work to make the report look complete.

## Do not act

This skill is read-only. Do not edit code, create commits, push changes, reply to
comments, resolve threads, dismiss reviews, stage new review comments, submit a
review, approve, or request changes.

## Report

Report one row per unique unresolved thread with its disposition, impact when
applicable, confidence, reviewer, file and line when available, a concise
rationale, and the requested or implied next step. Include links to the original
comments.

Order the report by disposition, with **Action required** first, then by impact
from strategic to nit. Summarize counts by disposition and call out any comments
that could not be fetched or verified. Clearly state that no action was taken.
