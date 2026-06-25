# Defect Taxonomy

Initial taxonomy for the personal QA Skillset. Treat this as a starting point and update it with real defect data.

## Defect Types

| Type | Meaning | Common Test Gap |
|---|---|---|
| calculation_error | Incorrect computation, aggregation, rounding, or formula selection. | Missing expected oracle, boundary case, reconciliation check. |
| data_integrity_error | Data is inconsistent across tables, screens, exports, or reruns. | Missing cross-artifact consistency check. |
| period_boundary_error | Incorrect behavior at fiscal period, month, quarter, or year boundaries. | Missing start/end date and retrospective adjustment cases. |
| state_transition_error | Invalid lifecycle transition or missing status update. | Missing state transition table or invalid transition tests. |
| authorization_error | Role, permission, approval, or segregation-of-duty failure. | Missing role matrix and negative permission tests. |
| validation_error | Input validation accepts invalid data or rejects valid data. | Missing equivalence partitioning and boundary value tests. |
| integration_error | Failure across API, file import/export, batch, or external system boundary. | Missing contract, mapping, retry, or error handling tests. |
| concurrency_error | Race, duplicate processing, locking, or stale data issue. | Missing parallel operation and retry tests. |
| idempotency_error | Rerun, retry, or duplicate submission changes result incorrectly. | Missing repeat execution tests. |
| reporting_error | Screen/report/export values, labels, filters, or layouts are wrong. | Missing report reconciliation and filter combination tests. |
| migration_error | Incorrect data migration or backward compatibility issue. | Missing before/after comparison and rollback checks. |
| environment_config_error | Behavior depends on configuration, locale, timezone, or deployment setting. | Missing environment matrix tests. |
| usability_error | User flow is confusing, blocked, or misleading. | Missing exploratory or workflow-based testing. |
| observability_error | Failure cannot be diagnosed because logs, messages, or audit trails are insufficient. | Missing audit/log verification. |

## Quality Characteristics

| Characteristic | QA Meaning |
|---|---|
| functional_correctness | Results match requirements and business rules. |
| data_integrity | Data remains complete, consistent, and reconcilable. |
| reliability | Behavior is stable across retries, reruns, failures, and recovery. |
| security | Access, input handling, and sensitive data behavior are safe. |
| auditability | Important operations leave sufficient trace for audit and investigation. |
| maintainability | Tests and product behavior are understandable and changeable. |
| operability | Operations can monitor, diagnose, and recover from failures. |
| usability | Target users can complete workflows without avoidable confusion. |
| compatibility | Behavior holds across supported browsers, OS, integrations, locales, or versions. |
| performance | Response time, throughput, or batch duration remains acceptable. |

## Trigger Conditions

Use these as tags when analyzing defects.

- fiscal_year_start
- fiscal_year_end
- month_end
- leap_day
- timezone_boundary
- no_data
- single_record
- many_records
- duplicate_data
- retrospective_adjustment
- correction_after_approval
- import_export_roundtrip
- rerun
- retry_after_failure
- concurrent_update
- permission_denied
- role_change
- approval_workflow
- locale_change
- decimal_rounding
- currency_conversion
- filter_combination
- sort_order
- stale_cache

## Detection Phases

| Phase | Meaning |
|---|---|
| requirements_review | Found before implementation during requirement/design review. |
| unit_test | Found by developer-level tests. |
| integration_test | Found across components or services. |
| system_test | Found in QA system testing. |
| acceptance_test | Found during UAT or business validation. |
| production | Escaped to production. |
| unknown | Detection phase unavailable. |

## Root Cause Categories

| Category | Meaning |
|---|---|
| requirement_ambiguity | Requirement or acceptance criteria did not define expected behavior. |
| test_design_gap | Test design omitted relevant condition, combination, or oracle. |
| implementation_defect | Code did not implement known expected behavior. |
| data_setup_gap | Test data did not include necessary condition. |
| environment_gap | Test environment did not match relevant production condition. |
| review_gap | Static review missed an issue visible before execution. |
| automation_gap | Automated tests were missing, flaky, or asserted the wrong thing. |
| regression_selection_gap | Existing tests existed but were not selected for the change. |
| monitoring_gap | Production detection or diagnosis was delayed by poor observability. |
