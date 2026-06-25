# MVP Validation Checklist

Use this checklist before treating the prototype as ready for real-data trial.

## Structure

- [ ] `.claude-plugin/plugin.json` exists.
- [ ] `README.md` explains purpose, skills, flow, and safety boundary.
- [ ] All three skills have `SKILL.md`.
- [ ] Each skill has step files.
- [ ] Each skill has a checklist.
- [ ] Shared references are under `references/`.
- [ ] Output templates are under `templates/`.
- [ ] End-to-end guide exists.
- [ ] Input/output contracts exist.

## Skill Chain

- [ ] `defect-trend-analysis` produces `defect-pattern-catalog.yaml`.
- [ ] `derive-regression-viewpoints` consumes `defect-pattern-catalog.yaml`.
- [ ] `derive-regression-viewpoints` produces `selected-regression-viewpoints.yaml`.
- [ ] `test-design-review` consumes regression viewpoints.
- [ ] `test-design-review` produces actionable findings.

## Evidence Discipline

- [ ] Every defect pattern has evidence.
- [ ] Every root-cause hypothesis is marked direct/inferred/claimed.
- [ ] Every regression viewpoint cites source patterns and defects.
- [ ] Every review finding cites a viewpoint, pattern, or source section.
- [ ] Unknown coverage is not mislabeled as missing.

## QA Usefulness

- [ ] Outputs are understandable to a QA engineer without reading all source files.
- [ ] Findings identify exact missing conditions.
- [ ] Recommendations can be turned into test cases.
- [ ] Accounting/business application risks are represented:
  - [ ] period boundary
  - [ ] data integrity
  - [ ] authorization
  - [ ] auditability
  - [ ] rerun/idempotency
  - [ ] output reconciliation
  - [ ] concurrency

## Safety

- [ ] No hooks are included.
- [ ] No MCP servers are included.
- [ ] No external CLI is required.
- [ ] No network access is required.
- [ ] Real defect data must be masked before use.
- [ ] Customer names, account names, and production identifiers are removed or replaced.

## Real-Data Trial Readiness

- [ ] Defect CSV format is agreed.
- [ ] 50-100 defects are exported.
- [ ] Sensitive fields are masked.
- [ ] Existing test case inventory is available or explicitly unavailable.
- [ ] Review target test design is selected.
- [ ] Human reviewer will judge whether findings are useful.
