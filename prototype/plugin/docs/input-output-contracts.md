# Input / Output Contracts

This document defines the file contracts between MVP skills.

## Contract Summary

| Producer | Output | Consumer | Required |
|---|---|---|---|
| `defect-trend-analysis` | `defect-trend-report.md` | Human reviewer | Yes |
| `defect-trend-analysis` | `defect-pattern-catalog.yaml` | `derive-regression-viewpoints`, `test-design-review` | Yes |
| `derive-regression-viewpoints` | `regression-viewpoint-catalog.md` | Human reviewer, `test-design-review` | Yes |
| `derive-regression-viewpoints` | `selected-regression-viewpoints.yaml` | `test-design-review` | Yes |
| `derive-regression-viewpoints` | `missing-regression-tests.md` | Human reviewer | Recommended |
| `test-design-review` | `test-design-review-report.md` | Human reviewer | Yes |

## Defect Input

Recommended CSV:

```csv
id,title,description,feature,detected_phase,severity,root_cause,fix_summary,related_pr,test_gap
```

Field contract:

| Field | Required | Purpose |
|---|---|---|
| `id` | Yes | Stable evidence source ID |
| `title` | Yes | Quick defect identification |
| `description` | Yes | Failure condition and observed behavior |
| `feature` | Recommended | Affected area mapping |
| `detected_phase` | Recommended | Escape and quality gate analysis |
| `severity` | Recommended | Risk prioritization |
| `root_cause` | Optional | Direct or claimed RCA input |
| `fix_summary` | Optional | Inference source for root cause and regression |
| `related_pr` | Optional | Code/change traceability |
| `test_gap` | Optional | Direct or claimed missing test evidence |

## `defect-pattern-catalog.yaml`

Required top-level fields:

```yaml
version: 0.1
generated_from:
  sources: []
patterns: []
```

Required pattern fields:

```yaml
id: PAT-001
name: ""
display_name: ""
summary: ""
status: active
risk_level: high
confidence: supported
affected_quality_characteristics: []
defect_ids: []
affected_areas: []
common_conditions: []
likely_root_causes: []
recommended_regression_viewpoints: []
evidence: []
```

Rules:

- `id` must be stable.
- `defect_ids` must reference source defects.
- `evidence` must not be empty.
- `risk_level` should be qualitative: `critical`, `high`, `medium`, `low`.
- `confidence` should be `confirmed`, `supported`, `hypothesis`, or `unknown`.

## `selected-regression-viewpoints.yaml`

Required top-level fields:

```yaml
version: 0.1
selection_context: {}
selected: []
not_selected: []
```

Required selected viewpoint fields:

```yaml
viewpoint_id: RV-001
title: ""
priority: high
source_patterns: []
source_defects: []
applicable_areas: []
cadence: []
selection_reason: []
coverage: unknown
```

Rules:

- `source_patterns` must reference `PAT-*` IDs.
- `source_defects` should preserve traceability to source defects.
- `coverage` must be one of `covered`, `partial`, `missing`, `unknown`.
- Use `unknown` when test case inventory is unavailable.

## `test-design-review-report.md`

Required sections:

- Review Summary
- Review Context
- Current Test Design Coverage
- Regression Viewpoint Coverage
- Findings
- Recommended Test Additions
- Open Questions

Required finding fields:

```text
Severity
Category
Related viewpoint
Evidence type
Source
Why It Matters
Required Change
```

Rules:

- Critical/high findings require explicit action.
- Findings must not be generic.
- A finding should cite a section, test case ID, viewpoint ID, or defect pattern.
