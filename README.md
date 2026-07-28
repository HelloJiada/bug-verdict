# Bug Verdict

An evidence-first bug investigation workflow skill for cases where the cause is unclear and guessing would create rework.

面向根因尚不明确的软件问题，提供以证据为先的 Bug 调查工作流，避免猜测导致的返工。

## When to Use

Use Bug Verdict when a software issue has an unclear cause and likely requires runtime evidence, state tracing, asynchronous reasoning, or cross-boundary investigation before code changes.

Typical examples include:
- loading that never completes
- stale or inconsistent state after navigation, retry, or return flows
- crashes, hangs, or intermittent failures
- UI mismatches whose cause is not yet known
- external integration failures with unclear responsibility boundaries
- performance or stability regressions without a confirmed bottleneck

## 何时使用

当软件问题的根因尚不明确，并且在修改代码前可能需要运行时证据、状态追踪、异步推理或跨边界调查时，使用 Bug Verdict。

典型示例包括：
- 加载始终无法完成
- 导航、重试或返回流程后状态陈旧或不一致
- 崩溃、卡死或间歇性故障
- 根因尚不明确的 UI 不一致
- 责任边界不清晰的外部集成故障
- 未确认瓶颈的性能或稳定性回归

## When Not to Use

Do not use Bug Verdict when the intended change and implementation point are already clear.

Typical non-cases include:
- straightforward feature work
- explicit copy replacement
- explicit spacing, color, asset, or radius changes with no causal ambiguity
- direct refactors requested for maintainability rather than investigation

## 何时不要使用

当预期变更和实现位置已经明确时，不要使用 Bug Verdict。

典型非适用场景包括：
- 直接明确的功能开发
- 明确的文案替换
- 没有因果歧义的明确间距、颜色、资源或圆角调整
- 出于可维护性而提出的直接重构，而不是调查工作

## Core Principles

- No root-cause verdict without evidence.
- No code changes without fix authorization.
- No closeout without path-based verification.
- Suspicious code is a clue, not a verdict.
- If evidence is insufficient, say so explicitly.

## 核心原则

- 没有证据，不下根因结论。
- 没有修复授权，不修改代码。
- 没有基于路径的验证，不结束调查。
- 可疑代码只是线索，不是结论。
- 如果证据不足，要明确说明。

## Install

Copy the `bug-verdict/` directory into your local skills directory.

## 安装

将 `bug-verdict/` 目录复制到本地 skills 目录。

## First Invocation

```text
/bug-verdict The loading spinner never disappears after retry.
/bug-verdict Payment succeeded, but the entitlement did not refresh.
/bug-verdict The design shows rounded corners, but the running UI still renders square corners.
/bug-verdict Investigate only: state becomes stale after returning from checkout.
```

## 首次调用

```text
/bug-verdict 重试后加载指示器始终不消失。
/bug-verdict 支付已成功，但权益没有刷新。
/bug-verdict 设计稿显示圆角，但运行中的 UI 仍然显示直角。
/bug-verdict 仅进行调查：从结账页面返回后状态变得陈旧。
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

## 工作流范围

Bug Verdict 覆盖完整的调查闭环：
- 案例受理
- 证据保全
- 链路调查
- 假设验证
- 根因裁决
- 修复执行控制
- 验证与结案
- 案例经验沉淀

## How to Adapt It to Your Project

Replace project-specific inputs with your own equivalents:
- `project-specific domain docs` for system context
- `project case library` for verified prior cases
- `runtime logs`, traces, screenshots, API responses, and recordings for evidence
- `user acceptance` or `business acceptance` for final validation roles

## 如何适配到你的项目

将项目特定的输入替换为你自己的对应内容：
- 用于系统上下文的 `project-specific domain docs`
- 用于已验证历史案例的 `project case library`
- 用于证据的 `runtime logs`、链路、截图、API 响应和录屏
- 用于最终验证角色的 `user acceptance` 或 `business acceptance`

## Why This Exists

Bug Verdict exists to stop the guess-fix-rework cycle. It gives investigation work a repeatable structure so teams can distinguish clues from proof, protect scope, and avoid calling a bug fixed before the original path has been verified.

## 为什么存在

Bug Verdict 旨在阻止猜测—修复—返工循环。它为调查工作提供可重复的结构，使团队能够区分线索与证据、保护范围，并避免在原始路径尚未验证前就宣布问题已修复。
