# Real Data Trial Checklist

Use this checklist for the first 50-100 defect real-data trial.

## Before Export

- [ ] Trial owner is identified.
- [ ] Source system is identified.
- [ ] Export scope is defined:
  - [ ] date range
  - [ ] release range
  - [ ] severity range
  - [ ] modules/features
- [ ] Permission to use sanitized data in this workspace is confirmed.

## Export

- [ ] Defects exported to CSV.
- [ ] Required columns are present:
  - [ ] `id`
  - [ ] `title`
  - [ ] `description`
  - [ ] `feature`
  - [ ] `severity`
- [ ] Recommended columns are included where available:
  - [ ] `detected_phase`
  - [ ] `escaped`
  - [ ] `root_cause`
  - [ ] `fix_summary`
  - [ ] `test_gap`
  - [ ] `business_impact`
  - [ ] `existing_test_case_id`

## Masking

- [ ] `masking-rules.md` was applied.
- [ ] Stable masked IDs are used.
- [ ] Customer names are removed.
- [ ] Person names are removed.
- [ ] Production URLs and IDs are removed.
- [ ] Secrets and credentials are removed.
- [ ] A human performed a final scan.

## Data Quality

- [ ] 50-100 records remain after filtering.
- [ ] At least 5 high or critical defects are included, if available.
- [ ] Production/UAT escaped defects are included, if available.
- [ ] Multiple modules/features are represented.
- [ ] Descriptions still contain concrete failure conditions.
- [ ] Rows with insufficient data are marked, not silently fixed.

## Run `defect-trend-analysis`

- [ ] Input coverage is reported.
- [ ] Excluded records are listed with reason.
- [ ] Patterns have evidence.
- [ ] Hypotheses are not presented as confirmed.
- [ ] Output files are created:
  - [ ] `defect-trend-report.md`
  - [ ] `defect-pattern-catalog.yaml`

## Run `derive-regression-viewpoints`

- [ ] All critical/high patterns are considered.
- [ ] Regression viewpoints cite source patterns and defects.
- [ ] Cadence is specified.
- [ ] Coverage is `unknown` unless existing tests are provided.
- [ ] Output files are created:
  - [ ] `regression-viewpoint-catalog.md`
  - [ ] `selected-regression-viewpoints.yaml`
  - [ ] `missing-regression-tests.md`

## Run `test-design-review`

- [ ] Review target is selected.
- [ ] Regression viewpoints are loaded.
- [ ] Findings are specific and actionable.
- [ ] Critical/high findings have required changes.
- [ ] Output file is created:
  - [ ] `test-design-review-report.md`

## Human Evaluation

Rate the outputs:

| Question | Rating |
|---|---|
| Are the defect patterns recognizable to QA/domain experts? | 1-5 |
| Are the regression viewpoints testable? | 1-5 |
| Are the review findings actionable? | 1-5 |
| Did the workflow find something not already explicit in the test design? | 1-5 |
| Is the evidence traceable enough to trust? | 1-5 |

## Decision

Choose one:

- [ ] Continue to Phase9 with a real trial.
- [ ] Improve data format and rerun Phase8.
- [ ] Adjust taxonomy/references before using real data.
- [ ] Stop because the output is not useful enough.
