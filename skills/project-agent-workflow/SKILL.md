---
name: project-agent-workflow
description: Generic Codex workflow for converted expert agents and Kiro hooks. Use when coordinating implementation, refactoring, spec validation, quality review, security review, change summaries, or when the user mentions the former Kiro agents such as spec-analyst, impl-coder, quality-guardian, security-sentinel, change-summarizer, issue-refiner, or agent-security-auditor.
---

# Project Agent Workflow

Use this skill to apply the Kiro agent and hook behavior in Codex. Keep Kiro
files and Codex mirrors aligned when agent or hook behavior changes. Codex
agent prompts are mirrored in `.agents/agents/`.

## Agent Prompts

Load the matching prompt before delegating or role-playing specialized work:

- `.agents/agents/spec-analyst.md`: validate specs, requirements, acceptance
  criteria, gaps, ambiguity, and implementation readiness.
- `.agents/agents/impl-coder.md`: implement features or refactors from a clear
  requirement.
- `.agents/agents/quality-guardian.md`: review code, write documentation, and
  create tests.
- `.agents/agents/security-sentinel.md`: audit code, architecture, privacy, and
  configuration before completion.
- `.agents/agents/change-summarizer.md`: summarize changes, impact, tests, and
  follow-ups.
- `.agents/agents/issue-refiner.md`: refine vague issues into actionable work.
- `.agents/agents/agent-security-auditor.md`: audit AI-agent ecosystem files,
  prompts, hooks, skills, MCP settings, and suspicious instructions.

## Codex Flow

1. For implementation/refactoring/feature work, load `flutter-expert` and
   `project-steering` first.
2. Validate requirements with `spec-analyst` when a spec or acceptance criteria
   exists under `.kiro/specs`.
3. Implement with the local codebase patterns and the steering references.
4. Review quality and add or update tests with `quality-guardian` behavior.
5. Perform a mandatory security/privacy pass with `security-sentinel` behavior
   before considering the task complete.
6. Summarize changes with `change-summarizer` behavior in the final response
   when the task involved code or configuration edits.

## Hook Conversion

Kiro `askAgent` hooks are represented for Codex as workflow gates:

- `references/spec-validation-gate.kiro.hook`: run before implementation when a
  task maps to a Kiro spec.
- `references/security-review-gate.kiro.hook`: run before completion after code
  changes.
- `references/agent-security-audit-on-create.kiro.hook`: run when creating new
  agent, skill, hook, steering, or MCP files.
- `references/agent-security-audit-on-edit.kiro.hook`: run when editing agent,
  skill, hook, steering, or MCP files.

Codex controls actual tool execution and sandbox approval. Treat these gates as
required review steps, not as permission to bypass approval or run destructive
commands.
