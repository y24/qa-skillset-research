---
name: derive-regression-viewpoints
description: Convert defect patterns into prioritized regression test viewpoints with rationale, applicability, test conditions, and machine-readable selection data.
trigger: explicit
---

# Derive Regression Viewpoints

Use this skill after `defect-trend-analysis` has produced a `defect-pattern-catalog.yaml`. It turns recurring defect patterns into reusable regression viewpoints and selection recommendations.

## Inputs

Required:

- `defect-pattern-catalog.yaml`

Optional:

- existing test cases
- feature/module list
- requirement IDs
- changed files or PR summary
- release/time budget

## Outputs

Create or update:

- `regression-viewpoint-catalog.md`
- `selected-regression-viewpoints.yaml`
- `missing-regression-tests.md`

## Required References

Read these before deriving viewpoints:

- `../../references/regression-selection-criteria.md`
- `../../references/evidence-classification.md` if present

## Workflow

Follow the steps in order. Load one step at a time.

1. `steps/step-01-load-patterns.md`
2. `steps/step-02-prioritize-patterns.md`
3. `steps/step-03-derive-viewpoints.md`
4. `steps/step-04-map-coverage-gaps.md`
5. `steps/step-05-write-outputs.md`

## Rules

- Do not recommend "run all regression" as the main strategy.
- Every viewpoint must cite at least one defect pattern or source defect.
- Distinguish always-run, change-triggered, release-gate, and exploratory viewpoints.
- If existing test cases are unavailable, mark coverage as `unknown`, not `missing`.
- Keep suggested test conditions concrete enough for test design.

## Quality Gate

Before finishing, validate the output against `checklist.md`.
