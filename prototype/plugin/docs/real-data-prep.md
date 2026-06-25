# Real Data Preparation

Phase8 prepares the MVP for a small real-data trial with sanitized defect data.

## Target Trial Size

Start with 50-100 defects.

This is large enough to reveal repeated patterns but small enough for manual review of the output.

## Required Files

Prepare these inputs:

| File | Purpose |
|---|---|
| `real-defects.csv` | Sanitized historical defect list |
| `test-design.md` or exported test cases | Review target for `test-design-review` |
| optional `existing-test-cases.csv` | Helps classify coverage as covered/partial/missing instead of unknown |

Use [real-defects.template.csv](../../../samples/real-defects.template.csv) as the starting format.

## Recommended `real-defects.csv` Fields

| Field | Required | Description |
|---|---|---|
| `id` | Yes | Stable masked ticket ID, e.g. `BUG-0001` |
| `title` | Yes | Sanitized defect title |
| `description` | Yes | Sanitized observed behavior and failure condition |
| `feature` | Yes | Business feature, e.g. reporting, approval, import |
| `module` | Recommended | Larger system area |
| `component` | Recommended | Smaller technical or functional component |
| `detected_phase` | Recommended | `requirements_review`, `unit_test`, `system_test`, `acceptance_test`, `production`, etc. |
| `severity` | Recommended | Business/quality severity |
| `priority` | Optional | Triage priority |
| `escaped` | Recommended | `true` if escaped to production or customer/UAT after intended gate |
| `found_date` | Optional | Date found, use YYYY-MM-DD |
| `release` | Optional | Masked release name |
| `environment` | Optional | `dev`, `staging`, `uat`, `production`, etc. |
| `root_cause` | Optional | Root cause from ticket, if known |
| `fix_summary` | Optional | Sanitized summary of fix |
| `related_pr` | Optional | Masked PR/change ID |
| `existing_test_case_id` | Optional | Existing test case that should have caught it |
| `test_gap` | Recommended | Missing or weak test condition |
| `customer_impact` | Optional | Masked impact category, not customer name |
| `business_impact` | Recommended | e.g. financial_report_correctness, auditability, access_control |
| `masked_notes` | Optional | Notes about removed or generalized data |

## Allowed Values

### `detected_phase`

- `requirements_review`
- `design_review`
- `unit_test`
- `integration_test`
- `system_test`
- `acceptance_test`
- `uat`
- `production`
- `unknown`

### `severity`

- `Critical`
- `High`
- `Medium`
- `Low`
- `Unknown`

### `escaped`

- `true`
- `false`
- `unknown`

### `business_impact`

Suggested categories:

- `financial_report_correctness`
- `data_integrity`
- `access_control`
- `auditability`
- `rerun_idempotency`
- `output_reconciliation`
- `integration_reliability`
- `usability`
- `performance`
- `unknown`

## Preparation Steps

1. Export candidate defects from the source system.
2. Remove or mask sensitive fields using `masking-rules.md`.
3. Normalize columns to `real-defects.template.csv`.
4. Keep 50-100 records for the first trial.
5. Include a mix of:
   - production escaped defects
   - system test defects
   - acceptance/UAT defects
   - high and medium severity defects
   - several feature areas
6. Run a quick manual scan for remaining secrets or customer names.
7. Save the sanitized file outside version control if it contains sensitive internal data.

## First Trial Output Expectations

The first real-data trial should produce:

- 5-15 defect patterns
- 5-15 regression viewpoints
- a small number of high-signal review findings

If the output produces too many patterns:

- merge patterns that only differ by module name
- prefer failure condition over implementation detail
- raise the threshold for low-risk singletons

If the output is too generic:

- add `test_gap`, `fix_summary`, and `business_impact`
- include escaped defect flags
- preserve sanitized but concrete failure conditions
