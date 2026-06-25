# Regression Selection Criteria

Use this reference to select and prioritize regression viewpoints.

## Priority Drivers

| Driver | Why It Matters |
|---|---|
| risk_level | Critical/high patterns should influence release gates. |
| production_escape | Production defects indicate existing prevention failed. |
| defect_count | Repeated defects are strong candidates for reusable viewpoints. |
| financial_correctness | Incorrect financial values require high scrutiny. |
| data_integrity | Data inconsistency can create downstream defects and audit issues. |
| security | Authorization and role defects can become critical even with low count. |
| auditability | Missing audit trails may not be visible in normal UI testing. |
| change_relevance | Changed areas should trigger focused regression. |
| low automation confidence | Manual/exploratory coverage may be required when automation is weak. |

## Priority Mapping

| Condition | Viewpoint Priority |
|---|---|
| critical risk, security, or data integrity | critical |
| high risk or production defect | high |
| repeated medium defects affecting financial correctness | high |
| medium risk with clear reusable condition | medium |
| single low-risk pattern with thin evidence | low or monitor |

## Cadence Types

| Cadence | Meaning |
|---|---|
| smoke_if_changed | Run in smoke/sanity when affected area changes. |
| change_triggered | Run when related module, API, data model, or workflow changes. |
| release_gate | Run before release because business risk is high. |
| nightly_or_scheduled | Run periodically when execution cost is moderate/high. |
| exploratory_charter | Use as an exploratory session topic when automation is not enough. |
| monitor | Keep as known risk; select when related changes occur. |

## Selection Rules

1. Select all critical viewpoints for release-gate consideration.
2. Select high viewpoints when related area changes or before major release.
3. Select medium viewpoints when the affected feature changes, the test is cheap, or similar defects repeat.
4. Do not select a viewpoint only because it exists; record the trigger.
5. If no change context is provided, produce a baseline recommendation rather than a change-specific plan.

## Coverage Labels

| Label | Meaning |
|---|---|
| covered | Existing tests clearly cover the viewpoint. |
| partial | Existing tests cover some conditions but not the full viewpoint. |
| missing | Evidence shows the viewpoint is not covered. |
| unknown | Existing test cases were not provided or cannot be mapped. |

## Minimum Viewpoint Fields

```yaml
id: RV-001
title: ""
priority: high
source_patterns: []
source_defects: []
applicable_areas: []
test_conditions: {}
cadence: []
selection_reason: []
coverage: unknown
```
