# Changelog

[README](README.md)

[中文更新记录 / Chinese Changelog](CHANGELOG.zh-CN.md)

## v0.1.0 - 2026-07-29

### Added
- split the public docs into language-specific README files
- added dedicated changelog files for release history
- strengthened bug-verdict investigation rules for external-entry and Activity-lifecycle cases

### Changed
- moved update history out of README into CHANGELOG
- formalized partial-vs-whole-case verdict handling
- added a guardrail that root-cause convergence comes before worktrees, subagent orchestration, or complex implementation flow
- required every investigation round to state the current main hypothesis, falsification condition, and next minimal validation
- added a questioning threshold so investigations ask only for missing critical evidence, missing authorization, or undefined business expectation, and forbid fragmented repeated micro-questions
