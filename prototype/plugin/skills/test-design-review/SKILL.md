---
name: test-design-review
description: Review a test design against requirements, defect patterns, regression viewpoints, and QA design techniques. Produces actionable findings with evidence and coverage gaps.
trigger: explicit
---

# Test Design Review

Use this skill when reviewing a test design document, test case table, or QA plan. It checks whether the design covers high-risk defect patterns and regression viewpoints.

## Inputs

Required:

- test design document or test case list

Recommended:

- `defect-pattern-catalog.yaml`
- `regression-viewpoint-catalog.md`
- `selected-regression-viewpoints.yaml`
- requirements or acceptance criteria

## Outputs

Create or update:

- `test-design-review-report.md`

## Required References

Read these before reviewing:

- `../../references/test-design-review-checklist.md`
- `../../references/evidence-classification.md` if present
- `../../references/defect-taxonomy.md` if present

## Workflow

Follow the steps in order. Load one step at a time.

1. `steps/step-01-load-review-context.md`
2. `steps/step-02-map-test-design.md`
3. `steps/step-03-check-regression-viewpoints.md`
4. `steps/step-04-write-findings.md`
5. `steps/step-05-write-report.md`

## Review Rules

- Findings must be specific and actionable.
- Do not say "add more tests" without naming the missing condition.
- Tie findings to source lines/sections when possible.
- Use severity based on business risk, not only number of missing tests.
- Separate missing coverage from unknown coverage.
- Preserve evidence type: direct, inferred, claimed.

## Quality Gate

Before finishing, validate the report against `checklist.md`.
