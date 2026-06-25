# Phase8 Real Data Preparation

## Scope

Phase8 prepared the MVP for a sanitized real-data trial.

Added:

- `samples/real-defects.template.csv`
- `prototype/plugin/docs/real-data-prep.md`
- `prototype/plugin/docs/masking-rules.md`
- `prototype/plugin/docs/real-data-trial-checklist.md`

## Purpose

The MVP is now ready to accept a small real-data defect export after masking and normalization.

## Recommended First Trial

Use 50-100 sanitized defects with:

- stable masked IDs
- concrete failure descriptions
- feature/module labels
- severity
- detected phase
- escaped flag when available
- root cause or fix summary when available
- test gap when available
- business impact category

## Safety Boundary

Do not put raw exported defects into the repository if they contain sensitive data.

Before running the MVP:

- remove customer names
- remove person names
- remove emails and contact data
- remove production URLs and IDs
- remove secrets, tokens, credentials
- replace sensitive identifiers with stable placeholders

## Trial Readiness

| Area | Status |
|---|---|
| CSV template | Ready |
| Masking rules | Ready |
| Trial checklist | Ready |
| MVP docs | Ready |
| Actual real data | Not yet provided |
| Real-data run | Pending Phase9 |

## Recommended Phase9

Run the MVP on 50-100 sanitized real defects:

1. Create `real-defects.csv` from the template.
2. Run `defect-trend-analysis`.
3. Review the generated patterns with a human QA/domain reviewer.
4. Run `derive-regression-viewpoints`.
5. Apply `test-design-review` to a real or representative test design.
6. Decide whether the outputs are useful enough to evolve into a personal plugin.
