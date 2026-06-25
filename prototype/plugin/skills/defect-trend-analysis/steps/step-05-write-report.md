# Step 05: Write Report

## Goal

Produce both a human-readable report and a machine-readable defect pattern catalog.

## Instructions

1. Write `defect-trend-report.md` using the report structure from `templates/defect-trend-report.md` if present.
2. Write `defect-pattern-catalog.yaml` using stable IDs:
   - `PAT-001`
   - `RC-001`
   - `RV-001`
3. Include only supported claims in the summary.
4. Put unsupported but important ideas under `Open Questions` or `Hypotheses`.
5. End with recommended next actions:
   - regression viewpoints to create
   - review checklist items to add
   - data fields to improve

## Completion Criteria

- Markdown report is readable without the YAML.
- YAML catalog can be consumed by `derive-regression-viewpoints`.
- Every pattern has evidence.
- Every recommendation traces back to a pattern or source defect.
