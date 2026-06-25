# End-to-End Usage

This guide shows how to run the MVP workflow manually.

## 1. Prepare Defect Data

Use a CSV or Markdown table with these recommended fields:

```csv
id,title,description,feature,detected_phase,severity,root_cause,fix_summary,related_pr,test_gap
```

Minimum usable fields:

- `id`
- `title`
- `description`
- `feature`
- `severity`

Better analysis requires:

- detected phase
- root cause
- fix summary
- related PR
- test gap
- escaped/production flag
- existing test case ID

## 2. Run `defect-trend-analysis`

Input:

- defect CSV or bug list

Expected outputs:

- `defect-trend-report.md`
- `defect-pattern-catalog.yaml`

Acceptance criteria:

- Every pattern has evidence.
- Root causes are marked direct/inferred/claimed.
- Recommendations trace to defect IDs.

Sample PoC:

- input: `samples/defects.sample.csv`
- output: `prototype/output/defect-pattern-catalog.yaml`

## 3. Run `derive-regression-viewpoints`

Input:

- `defect-pattern-catalog.yaml`

Optional:

- existing test cases
- changed areas
- release scope

Expected outputs:

- `regression-viewpoint-catalog.md`
- `selected-regression-viewpoints.yaml`
- `missing-regression-tests.md`

Acceptance criteria:

- Every viewpoint cites pattern IDs and defect IDs.
- Cadence is explicit: `release_gate`, `change_triggered`, `smoke_if_changed`, `monitor`, or similar.
- Existing coverage is not called missing unless evidence exists.

Sample PoC:

- input: `prototype/output/defect-pattern-catalog.yaml`
- output: `prototype/output/regression-viewpoint-catalog.md`

## 4. Run `test-design-review`

Inputs:

- test design document or test case list
- `defect-pattern-catalog.yaml`
- `regression-viewpoint-catalog.md`
- `selected-regression-viewpoints.yaml`

Expected output:

- `test-design-review-report.md`

Acceptance criteria:

- Findings are specific.
- Each finding links to a viewpoint or defect pattern.
- Each finding has severity, evidence type, and required change.
- The report separates missing coverage from unknown coverage.

Sample PoC:

- input: `samples/test-design.sample.md`
- output: `prototype/output/test-design-review-report.md`

## 5. Interpret the Result

The MVP is successful when:

- historical defects become named patterns
- patterns become regression viewpoints
- viewpoints expose concrete gaps in a test design
- the review output contains actionable test additions

The MVP is not intended to:

- replace human QA judgment
- guarantee full coverage
- automatically connect to ticket systems
- execute tests
- produce security or NFR reviews beyond the defect-derived scope
