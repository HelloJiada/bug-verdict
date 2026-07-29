---
name: bug-verdict
description: Use when a software issue has an unclear cause, likely spans runtime behavior or state transitions, and needs evidence-based investigation before code changes.
---

# Bug Verdict: A Standardized Bug Investigation Workflow

> **Iron Law:** No root-cause verdict without evidence. No code changes without fix authorization. No closeout without path-based verification. Violating the process is violating the workflow.

Bug Verdict is for problems where behavior is wrong but the cause is not yet known. Its goal is not to patch symptoms quickly. Its goal is to reach a reviewable root-cause verdict, apply the smallest justified fix, and close the case with real verification evidence.

## 1. Scope and Non-Scope

| Accept | Reject and use a normal implementation flow |
| --- | --- |
| Crashes, hangs, loading that never completes, stale state, async races, cache inconsistencies, external integration failures, performance regressions | Straightforward implementation requests where the desired behavior and change location are already explicit |
| UI mismatches where the visual problem is real but the cause is unknown | Direct copy, spacing, color, asset, or radius replacements with no causal ambiguity |
| Behavior that differs by mode, account, environment, retry path, timing, or navigation path | Routine refactors requested for maintainability rather than investigation |

**Boundary rule:** “Make this card use a 4dp corner radius” is not a Bug Verdict case. “The design shows a 4dp corner radius, but the running UI still renders square corners” is a Bug Verdict case, because layout, rendering, image processing, or state behavior may be involved.

### Early Direct-Implementation Routing

Route to a normal implementation flow in the first response when the request identifies the target outcome, the target control/resource or the single property to change, and no runtime, state, constraint, rendering, or asynchronous-causality question remains.

| Request shape | Correct route |
| --- | --- |
| Replace this unselected icon with this asset in one named surface | Confirm scope, then implement directly |
| Change this selected border from 1dp to 1.6dp | Confirm the target path, then implement directly |
| The design specifies 4dp corners but the rendered UI is still square | Open a Bug Verdict case because the cause is unknown |

## 2. Governing Principles

1. **Runtime facts outrank intuition.** What actually happened outranks what the code seems likely to do.
2. **Evidence outranks suspicious code.** A suspicious line is a clue, not a verdict.
3. **Use the smallest justified scope.** Do not bundle cleanup, refactors, or adjacent changes into a bug fix unless the same evidence proves the same cause.
4. **Authorization is separated by action.** Authorization to investigate is not authorization to modify code. Authorization to modify is not authorization to commit, push, delete, or alter local configuration.
5. **Verdicts must be reviewable.** A reader must be able to trace the conclusion back to the symptom, evidence, execution path, and verification result.
6. **Insufficient evidence must be stated plainly.** Do not fill gaps with confidence language.

## 3. Two-Axis Case Classification

### Same-Case Supplements vs. New Cases

Treat a later clarification as a **same-case supplement** when it adds a title, copy, acceptance criterion, display rule, or a narrower implementation requirement to an already investigated issue. Reuse the existing evidence, verdict, and authorization state. Update the scope and regression boundary; do not reopen intake, repeat evidence collection, or ask the same first diagnostic question.

Open a new case only when at least one observable condition is true:
- the new symptom occurs through a different entry point, host, or business object
- the existing verdict cannot explain the new symptom
- the new symptom requires different runtime evidence, a different execution path, or a different fix boundary
- the original case was closed and the new report proves an independent regression outside the verified path

Example: after resolving “a loading page exposes an address-like title,” a request to fix the title to a chosen label is a same-case supplement. It should reuse the original title-update boundary rather than reopen the investigation.

Every case must be classified on two axes: **impact priority** and **case size**.

### 3.1 Impact Priority

| Priority | Typical cases | Handling expectation |
| --- | --- | --- |
| P0 | System-wide crashes, blocked payments, blocked critical workflows, data corruption, severe security impact | Preserve evidence immediately, establish the failure path quickly, and do not lower the proof bar under urgency |
| P1 | Main-path failures such as loading never completing, entitlement not arriving, or the primary task being blocked | Investigate and reach a root-cause verdict in the current session if possible |
| P2 | Regressions in behavior or UI where the product still works but is wrong | Compare expected vs actual behavior and verify the affected path and closest adjacent boundaries |
| P3 | Non-blocking quality issues, low-frequency stability issues, minor visual drift, weak but real regressions | Record the case properly and avoid treating low severity as permission to guess |

### 3.2 Case Size

| Size | Standard | Investigation depth | Fix and regression expectation |
| --- | --- | --- | --- |
| Small | Single entry point, stable symptom, limited boundary ambiguity, likely 1–2 files | Minimal evidence, minimal tracing, one main hypothesis | One root-cause fix, original-path verification, and one closest boundary check |
| Medium | Multiple states, 2–5 files, one async boundary, one comparison path, or moderate ambiguity | Full case notes, explicit path tracing, evidence across entry/state/output boundaries | Minimal fix or tightly grouped fix for the same cause, plus adjacent regression coverage |
| Large | Cross-module, cross-mode, cache/lifecycle/environment interactions, weak-network or intermittent behavior, multiple async boundaries | Multi-round investigation, evidence preservation first, state or timing model if necessary | Staged verification, explicit uncovered risk, and no guess-based wide fixes |

### 3.3 Execution Matrix

| Combination | Priority of action |
| --- | --- |
| P0/P1 Small | Gather minimal decisive evidence fast, fix one proven cause, verify the original path |
| P0/P1 Medium | Preserve evidence broadly enough to avoid false fixes, then constrain scope before editing |
| P0/P1 Large | Treat as a dedicated investigation, not a quick patch; reversible containment may be acceptable, blind patching is not |
| P2/P3 Small | Keep the record concise, but do not skip evidence, authorization, or verification |
| P2/P3 Medium | Trace the full boundary that could explain the mismatch and verify nearby paths |
| P2/P3 Large | Stage the investigation and keep uncovered risk explicit |

## 4. Standard Procedure

You must complete the stages in order. Earlier stages may be recorded briefly for a small case, but they may not be skipped.

| Stage | Required input | Required output | Gate to next stage | Forbidden move |
| --- | --- | --- | --- | --- |
| 0. Open the case | User-reported symptom, impact, current authorization | Clear statement of the wrong behavior and expected behavior | The case is framed as an investigation target, not a guessed cause | Turning the symptom statement into a root-cause claim |
| 1. Establish context | Relevant system area | Read the governing docs, known boundaries, and nearby prior cases if they exist | The investigator understands the system boundary well enough to trace it | Searching random local code first when system docs exist |
| 2. Preserve evidence | Reproduction clues or captured evidence | Logs, traces, screenshots, recordings, API responses, timing, environment, account, version, or explicit evidence gaps | At least one real symptom proof or a clearly stated gap | Inventing logs, timelines, or responses |
| 3. Trace the path | Preserved evidence | Input → state → async boundary → output path, plus a first suspected failure boundary | A concrete candidate boundary is identified | Declaring a verdict after reading one function |
| 4. Test a hypothesis | Candidate boundary | One main hypothesis, one falsification condition, one minimal validation method | The hypothesis is supported or falsified by evidence | Editing multiple candidate points at once |
| 5. Issue a verdict | Validated hypothesis | Verdict reached / pending validation / insufficient evidence, plus reason | The verdict explains symptom, conditions, path, and evidence | Using “probably” language as fake certainty |
| 6. Execute an authorized fix | Verdict + fix authorization | Smallest justified change set and explicit non-goals | The diff stays within the proven cause boundary | Editing without authorization or bundling cleanup |
| 7. Verify and close out | Fix diff and testable path | Build/test checks, original-path verification, adjacent regression coverage, uncovered boundary report | Real evidence shows what is fixed and what was not checked | Calling it fixed on compilation alone |
| 8. Capture case law | Closed or partially closed case | Decide whether the case belongs in the project case library | Root cause, fix, and verification are reusable and reviewable | Writing speculation into reusable case law |

## 5. Evidence Rules

### 5.1 Admissibility

Evidence is admissible only if it is:
- **Authentic**: it comes from a real run, real code, real trace, real response, or confirmed user artifact
- **Relevant**: it explains the current case rather than a historical curiosity
- **Complete enough**: it preserves the surrounding context that gives it meaning
- **Reviewable**: someone else can trace where it came from and re-check it

### 5.2 Evidence Grades

| Grade | Evidence | What it can prove |
| --- | --- | --- |
| A: Direct evidence | Stable reproduction, stack trace, real API response, trace data, exact timing, confirmed screenshot or recording | Directly proves observed runtime facts |
| B: Supporting evidence | Call chains, state flow, nearby diffs, working comparisons, constraints, resource definitions, prior verified cases | Narrows the search and supports a verdict but usually cannot prove one alone |
| C: Clues | Suspicious variables, code smell, naming, intuition, model guesswork, half-remembered behavior | Only enough to form a hypothesis |

### 5.3 Conflicting Evidence

- If runtime evidence conflicts with static reading, believe the runtime first and explain the mismatch.
- If screenshots conflict with design expectations, verify state, device, version, and scenario before drawing conclusions.
- If historical docs conflict with current behavior, prefer current code and current runtime evidence.
- If evidence conflicts and cannot yet be reconciled, the verdict is **insufficient evidence**.

### 5.4 Existing Runtime Evidence First

Before adding any new instrumentation, ask whether runtime evidence already exists.

### 5.5 First-Round Instance-Level Logging for External Entry / Activity Lifecycle Cases

When a case involves **external entry points** such as Chrome, H5, ads, notifications, deep links, or intent filters, and the symptom looks like repeated navigation, duplicated page opens, needing multiple back presses to exit, unexpected `onNewIntent`, task-stack confusion, or Activity recreation, the **first** logging round must use an instance-level template. Do not spend a round on generic `action/data` logging and only later realize you still cannot tell whether instances were duplicated.

The first round must capture at least:

1. **Host Activity** instance identity at `onCreate`, `onNewIntent`, and the first business dispatch point (`processLogic`, `handleIntent`, `dispatch`, or equivalent).
2. **Target Activity** instance identity at `onCreate`, `onNewIntent`, `onBackPressed`, and `onDestroy`.
3. **Correlation keys** such as business entity IDs, source, and task ID.
4. **Dispatch sequence or dedupe result** so it is directly visible how many times the same logical entry was consumed.

Recommended shape:

```text
host_create instance=<id> ...
host_new_intent instance=<id> ...
host_dispatch instance=<id> ...
target_create instance=<id> entity=<id> ...
target_new_intent instance=<id> ...
target_back_pressed instance=<id> ...
target_destroy instance=<id> finishing=<bool> changingConfig=<bool>
```

The first-round objective is not merely to show that navigation happened twice. It is to answer, in one pass:
- did one host instance dispatch twice, or was a second host instance created?
- was the target Activity reused, or was a second target instance created?
- how many times was the same logical entity actually consumed?

If the first-round logs cannot answer those questions, the instrumentation boundary was wrong. Fix the logging boundary before moving to the next hypothesis.

### 5.6 Partial Verdicts Are Not Whole-Case Verdicts

If one reported problem actually contains multiple abnormal sub-paths, keep separate verdict states per sub-problem:

```text
[Sub-problem A] pending validation / verdict reached / insufficient evidence
[Sub-problem B] pending validation / verdict reached / insufficient evidence
[Whole-case status] only moves into fix execution once every load-bearing sub-problem has been adjudicated
```

A locally established cause does **not** automatically authorize whole-case implementation. If a fix for one sub-problem could mask, short-circuit, or otherwise contaminate evidence for another unresolved sub-problem, continue investigation first.

### 5.7 No Complex Execution Flow Before Root Cause Convergence

While the case still has unresolved main-path sub-problems, default to the simplest investigation setup:
- stay in the main workspace
- keep instrumentation minimal and case-specific
- keep the work focused on evidence, path tracing, and original-path validation

Do **not** escalate into worktrees, subagent orchestration, or multi-stage implementation workflow until the root cause for the relevant sub-problem or whole case has actually been adjudicated and implementation has been authorized.

This rule exists to prevent tooling and process overhead from overtaking the bug investigation itself.

### 5.8 Every Round Must State the Current Main Hypothesis Explicitly

After each new evidence round, produce an explicit update in this shape:

```text
[New evidence]
[Execution path traced so far]
[Main hypothesis]
[Falsification condition]
[Current verdict state]
[Next minimal validation]
```

If the main hypothesis changes, the change must be visible in the case record. Do not allow the investigation to drift silently across multiple explanations.

| Current situation | Required action | Forbidden shortcut |
| --- | --- | --- |
| Logs/traces already exist | Analyze them first and identify the exact missing boundary before adding anything new | Ignoring existing evidence and adding new logs immediately |
| No logs but runtime evidence is appropriate | Add the smallest temporary instrumentation that can validate the main hypothesis | Asking others to guess what logs might matter |
| Logs are the wrong tool | Switch to screenshots, recordings, API responses, traces, or controlled reproductions | Treating missing logs as permission to guess |

Temporary instrumentation must:
1. stay on the minimum relevant boundary
2. include correlation fields such as request ID, entity ID, mode, or timing markers
3. come with explicit reproduction instructions
4. remain until the case is either accepted as resolved or explicitly abandoned
5. be removed before commit unless a documented exception applies

## 6. Burden of Proof and Verdict Standards

| Claim | Burden of proof | Minimum standard |
| --- | --- | --- |
| “This is the root cause.” | The person making the claim | The cause explains the symptom, trigger conditions, path, and evidence |
| “This fix works.” | The person applying the fix | The original path has been re-verified with fresh evidence |
| “There is no regression.” | The person claiming safety | Covered boundaries are listed and uncovered ones are explicit |
| “The UI matches the design.” | The person claiming visual success | Real screenshots or direct user/business acceptance confirm it |
| “This belongs in case law.” | The person writing the record | Root cause, fix, and verification are all real and reusable |

Verdict states are only:
- **Verdict reached**
- **Pending validation**
- **Insufficient evidence**

## 7. Hypothesis Testing Protocol

1. Keep one **main hypothesis** per round.
2. State it in this form: `I believe X is the cause because A/B evidence explains C; if D happens, this hypothesis is false.`
3. Use one minimal validation method: logs, traces, controlled reproduction, one targeted code change, or a working comparison.
4. Change one variable at a time.
5. If the hypothesis fails, go back to investigation rather than stacking fixes.
6. If multiple rounds fail, preserve what has already been ruled out and state the new evidence gap clearly.

## 8. Domain Review Courts

### UI / Interaction
- **Check first:** design, screenshots, state, constraints, rendering path, real device behavior
- **Red line:** do not equate visual difference with root cause
- **Minimum regression:** default, selected, disabled, long content, device variance

### State Synchronization
- **Check first:** source of truth, write points, observation chain, refresh path, return path
- **Red line:** do not inspect only the display layer
- **Minimum regression:** create, update, return, re-enter, retry

### Async / Concurrency
- **Check first:** request start, callback, cancellation, retries, race windows, sequencing
- **Red line:** do not turn timing guesses into verdicts
- **Minimum regression:** rapid repetition, slow network, cancellation, overlapping actions

### Data / Cache
- **Check first:** data source, cache layer, invalidation, overwrite order, freshness assumptions
- **Red line:** do not confuse cache state with source-of-truth semantics
- **Minimum regression:** first load, refresh, identity switch, stale-cache recovery

### External Integrations
- **Check first:** SDK/API boundaries, callbacks, retries, error codes, environment differences
- **Red line:** do not treat third-party behavior as an evidence-free black box
- **Minimum regression:** success, failure, timeout, re-entry, partial response handling

### Performance / Stability
- **Check first:** timing chain, thread ownership, lock boundaries, resource release, pressure conditions
- **Red line:** do not say “optimize it” without locating the bottleneck
- **Minimum regression:** stress, low-end device or environment, long-running session, repeated operation

## 9. Fix Execution Controls

Before changing code, make these explicit:
- whether fix authorization has been granted
- which files and boundaries are in scope
- what will remain unchanged
- whether temporary instrumentation exists and when it will be removed
- what architectural or lifecycle constraints still apply

While editing:
- one verdict should map to one cause boundary
- do not widen scope without new evidence
- read both caller and callee when changing a boundary
- do not present UI or business correctness as solved until user or business acceptance exists where required

## 10. Verification, Closeout, and Case-Law Capture

### Verification Layers

| Layer | What it can prove | What it cannot prove |
| --- | --- | --- |
| Static checks / formatting | File correctness, syntax-adjacent issues, obvious structure problems | User-path correctness, runtime timing, visual fidelity |
| Build / tests | The code builds and covered tests pass | Real runtime behavior outside covered tests |
| Original-path verification | The reported path no longer fails in the same way | Safety across all neighboring paths |
| Adjacent regression coverage | The nearest related boundaries still behave correctly | Unchecked modes, environments, or integrations |
| User or business acceptance | The real target outcome is acceptable | All untested variants |

### Closeout Rule

A closeout report must state:
- what was verified
- what was not verified
- what risk remains
- whether user/business acceptance is still required
- whether the case belongs in the project case library

### Case-Law Capture Rule

Only capture a case as reusable case law if:
- the issue was real
- the root cause was actually established
- the fix was minimal and explicit
- the verification evidence is real and reviewable

## 11. Standard Briefs

### Case Intake Brief

```text
[Priority]
[Status: open / investigating / pending validation / verdict reached / closed]
[Observed behavior]
[Expected behavior]
[Current authorization]
[Known evidence]
[Evidence gap]
[Next step]
```

### Investigation Update

```text
[New evidence]
[Execution path traced so far]
[Main hypothesis]
[Falsification condition]
[Current verdict state]
[Next minimal validation]
```

### Verdict and Fix Request

```text
[Verdict]
[Evidence chain]
[Minimal fix]
[Unchanged boundaries]
[Regression risk]
[Requested authorization]
```

### Closeout Report

```text
[Resolved behavior]
[Root cause]
[Verification evidence]
[Adjacent regression coverage]
[Uncovered boundaries]
[Remaining risk]
[Case-law capture decision]
```

## 12. Anti-Patterns and Rejection Reasons

| Common move | Rejection reason |
| --- | --- |
| “It is obvious, just change this line.” | A suspicious line is only a clue until evidence proves it explains the failure path |
| “The code looks like the problem, so fix it.” | Reading alone is not a root-cause verdict |
| “We are in a hurry, add several refreshes and see.” | Urgency does not justify multi-point guess fixes |
| “It compiles, so it is fixed.” | Compilation proves buildability, not behavioral correctness |
| “Let’s fix similar places while we are here.” | Shared scope requires shared evidence |
| “The screenshot suggests spacing, so update the margin.” | Visual symptoms still need causal investigation |
| “There are no logs, let’s go by intuition.” | Missing logs mean preserve different evidence or add minimal instrumentation |
| “The change is small, no need to write regression boundaries.” | Small changes can still damage neighboring flows |

## 13. Invocation Examples

```text
/bug-verdict The loading spinner never disappears after retry.

/bug-verdict Payment succeeded, but the entitlement did not refresh; investigate only, do not modify code yet.

/bug-verdict P0: After checkout starts, all users are blocked from completing payment; logs and API responses are attached below.

/bug-verdict The design shows a 4dp corner radius, but the running UI still renders square corners; screenshots are attached below.

/bug-verdict The same workflow behaves differently across two runtime modes, and the cause is unknown.
```
