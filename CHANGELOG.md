# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### Added

- **16 new skills** covering the full development lifecycle:
  - **Define:** spec-driven-development, idea-refine
  - **Plan:** planning-and-task-breakdown
  - **Build:** incremental-implementation, test-driven-development, context-engineering, source-driven-development, frontend-ui-engineering, api-and-interface-design
  - **Verify:** browser-testing-with-devtools, debugging-and-error-recovery
  - **Ship:** git-workflow-and-versioning, ci-cd-and-automation, deprecation-and-migration, documentation-and-adrs, shipping-and-launch
- **7 new slash commands:** /spec, /plan, /build, /test, /review, /code-simplify, /ship
- **3 reference checklists:** testing-patterns, performance-checklist, accessibility-checklist
- **Anthropic best practices** reference guide moved to `references/anthropic-best-practices.md`

### Changed

- Rewrote `CLAUDE.md` to reflect new project structure, skills by phase, and full command listing
- Updated `writing-skills/SKILL.md` reference path for anthropic-best-practices.md
- Fixed `idea-refine` description to follow standard format (what + when to use)
- Removed stale `/mnt/skills/` script path from idea-refine

## [1.0.0] - 2025-04-14

### Added

- Initial marketplace release
- Marketplace configuration (`.claude-plugin/marketplace.json`)
- **8 core skills:** formatting-system-design, reviewing-architecture, reviewing-code, reviewing-logic, reviewing-performance, reviewing-security, stochastic-multi-agent-consensus, writing-skills
- **3 slash commands:** /add-commit-push, /commit-push, /setup-claude-md
- Session lifecycle hooks with desktop notifications
- Utility scripts for hooks and commands
