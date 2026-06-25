# Step 05: Write Outputs

## Goal

Write human-readable and machine-readable regression viewpoint outputs.

## Instructions

1. Write `regression-viewpoint-catalog.md`.
2. Write `selected-regression-viewpoints.yaml`.
3. Write `missing-regression-tests.md`.
4. Ensure every selected viewpoint links to source patterns and defect IDs.
5. Put lower-priority or context-dependent viewpoints under `not_selected` only if there is a concrete reason.

## Completion Criteria

- Markdown catalog is useful for test designers.
- YAML selection can be consumed by `test-design-review`.
- Missing coverage is not overstated when no existing test cases were provided.
- Recommendations distinguish cadence: smoke, change-triggered, release-gate, exploratory.
