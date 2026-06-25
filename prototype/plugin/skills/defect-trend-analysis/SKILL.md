---
name: defect-trend-analysis
description: Analyze historical defects or test failures, classify recurring patterns, separate evidence from hypotheses, and produce a defect trend report plus reusable defect pattern catalog.
trigger: explicit
---

# Defect Trend Analysis

Use this skill when the user provides defect tickets, bug CSVs, failure logs, incident summaries, or fix histories and wants recurring quality risks, root-cause hypotheses, and regression candidates.

## Outputs

Create or update:

- `defect-trend-report.md`
- `defect-pattern-catalog.yaml`

If the user specifies another output directory, write there. Otherwise ask only when no reasonable location is available.

## Required References

Read these before analyzing:

- `../../references/defect-taxonomy.md`
- `../../references/evidence-classification.md` if present

## Workflow

Follow the steps in order. Load one step at a time.

1. `steps/step-01-input-check.md`
2. `steps/step-02-classify-defects.md`
3. `steps/step-03-find-patterns.md`
4. `steps/step-04-root-cause-hypotheses.md`
5. `steps/step-05-write-report.md`

## Evidence Rules

- Separate facts, inferences, and hypotheses.
- Do not assign a root cause as confirmed unless the source explicitly supports it.
- Link each pattern to source defect IDs.
- Mark missing information as an open question instead of filling it with assumptions.
- Prefer qualitative risk labels over numeric scores unless the input provides measured data.

## Quality Gate

Before finishing, validate the output against `checklist.md`.
