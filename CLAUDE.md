# agent-skills

A collection of production-grade engineering skills for AI coding agents.

## Project Structure

```
skills/            → Core skills (SKILL.md per directory)
commands/          → Slash commands (markdown files with frontmatter)
references/        → Supplementary checklists and guides (testing, performance, accessibility, skill authoring)
hooks/             → Session lifecycle hooks (hooks.json)
scripts/           → Utility scripts used by hooks/commands
agents/            → Custom agent definitions
bin/               → Executables
output-styles/     → Output formatting templates
```

## Skills by Phase

**Define:** spec-driven-development, idea-refine
**Plan:** planning-and-task-breakdown
**Build:** incremental-implementation, test-driven-development, context-engineering, source-driven-development, frontend-ui-engineering, api-and-interface-design
**Verify:** browser-testing-with-devtools, debugging-and-error-recovery
**Review:** reviewing-code, reviewing-security, reviewing-architecture, reviewing-logic, reviewing-performance
**Ship:** git-workflow-and-versioning, ci-cd-and-automation, deprecation-and-migration, documentation-and-adrs, shipping-and-launch

**Cross-cutting:** formatting-system-design, writing-skills, stochastic-multi-agent-consensus

## Commands

- `/spec` — Write a structured specification before coding
- `/plan` — Break work into small verifiable tasks
- `/build` — Implement the next task incrementally
- `/test` — Run TDD workflow; for bugs, use the Prove-It pattern
- `/review` — Five-axis code review
- `/code-simplify` — Simplify code without changing behavior
- `/ship` — Pre-launch checklist for production deployment
- `/add-commit-push` — Stage, commit, and push (no Co-Authored-By)
- `/commit-push` — Commit staged changes and push (no Co-Authored-By)
- `/setup-claude-md` — Merge behavioral guidelines into a project's CLAUDE.md

## Conventions

- Every skill lives in `skills/<name>/SKILL.md`
- YAML frontmatter with `name` and `description` fields
- Description starts with what the skill does (third person), followed by trigger conditions ("Use when...")
- Every skill has: Overview, When to Use, Process, Common Rationalizations, Red Flags, Verification
- References are in `references/`, not inside skill directories
- Supporting files only created when content exceeds 100 lines

## Boundaries

- Always: Follow the skill-anatomy.md format for new skills
- Never: Add skills that are vague advice instead of actionable processes
- Never: Duplicate content between skills — reference other skills instead