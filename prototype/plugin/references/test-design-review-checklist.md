# Test Design Review Checklist

Use this reference when reviewing QA test designs.

## Core Review Dimensions

| Dimension | Check |
|---|---|
| traceability | Tests link to requirements, defect patterns, or risk viewpoints. |
| oracle | Expected results are measurable and not vague. |
| data conditions | Test data covers empty, single, multiple, boundary, and exceptional conditions. |
| boundaries | Date, amount, count, period, and status boundaries are explicit. |
| combinations | Important combinations are covered with decision tables or matrices. |
| state transitions | Workflow states and invalid transitions are covered. |
| negative paths | Permission denied, validation error, retry failure, and timeout are included. |
| regression rationale | Regression cases explain why they exist. |
| auditability | Important business events leave verifiable logs. |
| reconciliation | Values are checked across screens, reports, exports, APIs, and tables. |

## Accounting / Business Application Checks

| Area | Required Review Questions |
|---|---|
| period boundary | Are fiscal year start/end, month end, opening balance, and closing date included? |
| financial correctness | Is there an independently calculated expected value? |
| data integrity | Are detail totals reconciled with balance/report totals? |
| rerun/idempotency | Does repeating the process produce the same final state? |
| partial failure | Are retry and recovery scenarios tested? |
| authorization | Are role, workflow state, and session changes combined? |
| audit trail | Are logs checked for actor, timestamp, target, before/after, and reason? |
| output consistency | Do screen, report, Excel, and API values match? |
| concurrency | Are lock, wait, timeout, and parallel update conditions tested? |

## Finding Categories

- missing_regression_viewpoint
- partial_regression_viewpoint
- missing_oracle
- missing_boundary_condition
- missing_decision_combination
- missing_state_transition
- missing_audit_check
- missing_reconciliation_check
- unclear_traceability
- overbroad_or_ambiguous_case

## Severity Mapping

| Severity | Typical Condition |
|---|---|
| Critical | Missing security, authorization, data integrity, or financial close coverage. |
| High | Missing repeated defect pattern, financial correctness, auditability, or high-risk boundary coverage. |
| Medium | Missing important but scoped condition or expected result. |
| Low | Clarity, naming, or maintainability issue. |
