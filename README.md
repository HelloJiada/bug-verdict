# Bug Verdict

[中文说明 / Chinese README](README.zh-CN.md)

[Changelog](CHANGELOG.md)

An evidence-first bug investigation workflow skill for cases where the cause is unclear and guessing would create rework.

## When to Use

Use Bug Verdict when a software issue has an unclear cause and likely requires runtime evidence, state tracing, asynchronous reasoning, or cross-boundary investigation before code changes.

Typical examples include:
- loading that never completes
- stale or inconsistent state after navigation, retry, or return flows
- crashes, hangs, or intermittent failures
- UI mismatches whose cause is not yet known
- external integration failures with unclear responsibility boundaries
- performance or stability regressions without a confirmed bottleneck

## When Not to Use

Do not use Bug Verdict when the intended change and implementation point are already clear.

Typical non-cases include:
- straightforward feature work
- explicit copy replacement
- explicit spacing, color, asset, or radius changes with no causal ambiguity
- direct refactors requested for maintainability rather than investigation

## Core Principles

- No root-cause verdict without evidence.
- No code changes without fix authorization.
- No closeout without path-based verification.
- Suspicious code is a clue, not a verdict.
- If evidence is insufficient, say so explicitly.

## Install

Copy the `bug-verdict/` directory into your local skills directory.

## First Invocation

```text
/bug-verdict The loading spinner never disappears after retry.
/bug-verdict Payment succeeded, but the entitlement did not refresh.
/bug-verdict The design shows rounded corners, but the running UI still renders square corners.
/bug-verdict Investigate only: state becomes stale after returning from checkout.
```

## What the Workflow Covers

Bug Verdict covers the full investigation loop:
- case intake
- evidence preservation
- trace investigation
- hypothesis testing
- root-cause verdict
- fix execution control
- verification and closeout
- case-law capture

## How to Adapt It to Your Project

Replace project-specific inputs with your own equivalents:
- `project-specific domain docs` for system context
- `project case library` for verified prior cases
- `runtime logs`, traces, screenshots, API responses, and recordings for evidence
- `user acceptance` or `business acceptance` for final validation roles

## Why This Exists

Bug Verdict exists to stop the guess-fix-rework cycle. It gives investigation work a repeatable structure so teams can distinguish clues from proof, protect scope, and avoid calling a bug fixed before the original path has been verified.
