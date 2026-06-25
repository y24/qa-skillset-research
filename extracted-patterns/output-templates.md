# Output Templates

Phase3 output: target deliverable formats for the personal QA Skillset.

## Design Rules

- Markdown is for human review.
- YAML is for reuse by later skills.
- Every non-trivial claim must have an evidence type.
- Every recommendation should trace back to at least one defect, requirement, changed area, or review finding.
- Separate "confirmed facts" from "hypotheses".

## `defect-trend-report.md`

```markdown
# Defect Trend Report

## Summary

-対象期間:
-入力件数:
-分析対象外:
-主な傾向:
-重要リスク:

## Input Coverage

| Source | Count | Included | Excluded | Notes |
|---|---:|---:|---:|---|
| bugs.csv | 80 | 76 | 4 | 4件は再現手順なし |

## Top Defect Patterns

| Rank | Pattern | Count | Severity Mix | Affected Areas | Evidence Level | Recommended Action |
|---:|---|---:|---|---|---|---|
| 1 | Period boundary aggregation error | 7 | High: 3, Medium: 4 | Reporting, consolidation | direct | Add period boundary regression viewpoints |

## Pattern Details

### PAT-001: Period Boundary Aggregation Error

#### Description

What tends to fail and under which condition.

#### Evidence

| Source | Type | Observation | Reasoning |
|---|---|---|---|
| BUG-1234 | direct | Closing date total was incorrect | Shows period boundary calculation defect |

#### Common Conditions

- Fiscal year start/end
- Previous/current period comparison
- Retrospective adjustment
- No data / single row / multiple rows

#### Root Cause Hypotheses

| Hypothesis | Evidence Type | Reasoning | Verification Needed |
|---|---|---|---|
| Boundary condition omitted from test design | inferred | Multiple fixes mention missing end-date case | Compare past test cases |

#### Regression Candidates

- Verify totals across period start/end.
- Verify no-data and one-row cases.
- Verify rerun result consistency.

## Cross-Cutting Risks

| Risk | Related Patterns | Impact | Suggested Gate |
|---|---|---|---|
| Data integrity regression in financial reports | PAT-001, PAT-004 | Incorrect reporting output | Add report reconciliation check |

## Open Questions

| Question | Why It Matters | Needed Source |
|---|---|---|
| Which defects escaped to production? | Prioritizes regression suite | release/incident data |
```

## `defect-pattern-catalog.yaml`

```yaml
version: 0.1
generated_from:
  sources:
    - path: samples/defects.csv
      type: defect_csv
      included_records: 76
      excluded_records: 4

patterns:
  - id: PAT-001
    name: period_boundary_aggregation_error
    display_name: Period boundary aggregation error
    summary: Aggregation or comparison result is wrong around fiscal period boundaries.
    risk_level: high
    confidence: supported
    affected_quality_characteristics:
      - functional_correctness
      - data_integrity
      - auditability
    defect_ids:
      - BUG-1234
      - BUG-1892
    affected_areas:
      - consolidation
      - reporting
    common_conditions:
      - fiscal_year_start
      - fiscal_year_end
      - retrospective_adjustment
      - no_data
      - single_record
      - multiple_records
    likely_root_causes:
      - id: RC-001
        description: Boundary condition omitted from test design.
        evidence_type: inferred
        verification_needed: true
    recommended_regression_viewpoints:
      - id: RV-001
        title: Fiscal period boundary aggregation
        priority: high
        rationale: Multiple high-impact defects occurred around period boundaries.
    evidence:
      - source_id: BUG-1234
        type: direct
        observation: Closing date total was incorrect.
        reasoning: Directly demonstrates period-end aggregation failure.
        needs_verification: false
```

## `regression-viewpoint-catalog.md`

```markdown
# Regression Viewpoint Catalog

## RV-001: Fiscal Period Boundary Aggregation

### Priority

High

### Rationale

- Derived from: PAT-001
- Source defects: BUG-1234, BUG-1892
- Business impact: incorrect financial report values

### Applicable Areas

- Consolidation
- Reporting
- Export
- Journal generation

### Test Conditions

| Condition | Values |
|---|---|
| period position | start, middle, end |
| data volume | 0, 1, many |
| adjustment | none, retrospective |
| rerun | first run, rerun |

### Suggested Test Viewpoints

- Validate totals for previous/current/next period.
- Validate zero-data result is explicit and stable.
- Validate rerun produces the same output.

### Evidence

| Source | Type | Reasoning |
|---|---|---|
| PAT-001 | inferred | Repeated defects indicate missing period-boundary regression coverage |
```

## `selected-regression-viewpoints.yaml`

```yaml
version: 0.1
selection_context:
  target_release: ""
  changed_areas: []
  time_budget: ""

selected:
  - viewpoint_id: RV-001
    priority: high
    selection_reason:
      - related_defect_pattern: PAT-001
      - affected_by_change: true
      - business_criticality: high
    recommended_scope:
      - smoke_if_changed
      - full_before_release

not_selected:
  - viewpoint_id: RV-009
    reason: No related change and low recent defect frequency.
    revisit_when:
      - module_changes
      - similar_defect_found
```

## `test-design-review-report.md`

```markdown
# Test Design Review Report

## Review Summary

| Area | Result |
|---|---|
| Test basis coverage | Partial |
| Defect-history coverage | Missing key viewpoints |
| Boundary coverage | Needs improvement |
| Traceability | Partial |

## Findings

### F-001: Period-end aggregation cases are missing

- Severity: High
- Category: Missing regression viewpoint
- Related pattern: PAT-001
- Evidence type: direct
- Source: `test-design.md`, section "Aggregation tests"

#### Why It Matters

Past high-impact defects occurred at fiscal period boundaries. The current test design covers normal monthly aggregation but does not cover period start/end.

#### Required Change

Add test conditions for fiscal year start, fiscal year end, no-data, and retrospective adjustment.

## Coverage Matrix

| Viewpoint | Covered | Evidence | Gap |
|---|---|---|---|
| RV-001 Fiscal period boundary aggregation | No | N/A | Add tests |

## Open Questions

| Question | Impact |
|---|---|
| Are export totals reconciled with screen totals? | Needed for report consistency testing |
```

## `defect-trend-analysis` Minimal File Contract

The first PoC skill should write or update:

```text
output/
  defect-trend-report.md
  defect-pattern-catalog.yaml
```

The report must be useful even without the YAML. The YAML must be sufficient for `derive-regression-viewpoints`.
