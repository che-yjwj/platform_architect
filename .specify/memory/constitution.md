<!--
Sync Impact Report
- Version change: 0.0.0 -> 1.0.0
- Modified principles:
  - N/A (initial ratification from template)
- Added sections:
  - Engineering Standards
  - Delivery Workflow
- Removed sections:
  - Template placeholder principle and section tokens
- Templates requiring updates:
  - ✅ updated: .specify/templates/plan-template.md
  - ✅ updated: .specify/templates/spec-template.md
  - ✅ updated: .specify/templates/tasks-template.md
  - ⚠ pending: .specify/templates/commands/*.md (directory not present in repository)
- Follow-up TODOs:
  - TODO(RATIFICATION_DATE): Original adoption date not available in repository history.
-->
# Platform Architect Constitution

## Core Principles

### I. Code Quality Is Non-Negotiable
All production changes MUST pass static analysis, formatting, and peer review before merge.
Code MUST be readable, modular, and maintainable, with explicit error handling at integration
boundaries. New or changed logic MUST include documentation updates where behavior changes.
Rationale: strict quality gates reduce long-term defect cost and improve delivery speed.

### II. Test Evidence Before Merge
Every behavior change MUST include automated tests at the appropriate level (unit,
integration, and contract where interfaces change). Tests MUST fail before implementation and
pass after implementation. Flaky tests MUST be fixed or quarantined with a documented owner
and resolution date before release.
Rationale: deterministic test evidence prevents regressions and protects architecture decisions.

### III. User Experience Consistency
User-facing workflows MUST follow shared interaction patterns, terminology, and visual
conventions across screens and reports. Any new UI flow MUST define states for loading,
empty, error, and success outcomes. UX-impacting changes MUST include acceptance criteria that
are testable through scenario-based validation.
Rationale: consistency lowers user error rates and improves trust in architecture decisions.

### IV. Performance Budgets Are Required
Each feature MUST define measurable performance budgets before implementation and MUST verify
those budgets in validation artifacts before release. At minimum, teams MUST track latency,
throughput, and resource usage relevant to the feature scope. Regressions beyond agreed budgets
MUST block release unless explicitly approved with a documented mitigation plan.
Rationale: explicit budgets protect platform responsiveness and scalability as complexity grows.

## Engineering Standards

- Every spec MUST define quality gates, test scope, UX acceptance criteria, and performance
  targets.
- Every plan MUST include constitution checks mapped to all four core principles.
- Every task list MUST include work items for linting/review readiness, automated testing,
  UX consistency validation, and performance measurement.

## Delivery Workflow

- Feature work proceeds as: specification -> implementation plan -> tasks -> implementation ->
  validation evidence.
- Pull requests MUST include: requirement traceability, test evidence, UX validation notes (if
  user-facing), and performance results (if runtime-impacting).
- Releases MUST include a compliance review against this constitution.

## Governance

This constitution overrides conflicting local practices. Amendments require: (1) a written
proposal, (2) approval by project maintainers, (3) an update to dependent templates and runtime
guidance in the same change. Versioning follows semantic rules: MAJOR for incompatible
governance redefinitions/removals, MINOR for new principles or materially expanded mandates,
PATCH for clarifications that do not change obligations. Compliance reviews are required at
planning, pull request, and release checkpoints; unresolved violations MUST be tracked with
owners and due dates.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): Original adoption date not available in repository history. | **Last Amended**: 2026-03-04
