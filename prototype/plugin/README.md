# QA Defect Regression Review MVP

This prototype is a minimal QA skillset for converting historical defects into reusable regression viewpoints and applying them to test design review.

## MVP Flow

```text
defect data
  -> defect-trend-analysis
  -> defect-pattern-catalog.yaml
  -> derive-regression-viewpoints
  -> regression-viewpoint-catalog.md
  -> test-design-review
  -> test-design-review-report.md
```

## Skills

| Skill | Purpose | Primary Output |
|---|---|---|
| `defect-trend-analysis` | Analyze historical defects and classify recurring defect patterns. | `defect-trend-report.md`, `defect-pattern-catalog.yaml` |
| `derive-regression-viewpoints` | Convert defect patterns into prioritized regression viewpoints. | `regression-viewpoint-catalog.md`, `selected-regression-viewpoints.yaml`, `missing-regression-tests.md` |
| `test-design-review` | Review a test design against defect-derived regression viewpoints. | `test-design-review-report.md` |

## Directory Structure

```text
prototype/plugin/
  .claude-plugin/
    plugin.json
  skills/
    defect-trend-analysis/
    derive-regression-viewpoints/
    test-design-review/
  references/
    defect-taxonomy.md
    evidence-classification.md
    regression-selection-criteria.md
    test-design-review-checklist.md
  templates/
    defect-trend-report.md
    defect-pattern-catalog.yaml
    regression-viewpoint-catalog.md
    selected-regression-viewpoints.yaml
    missing-regression-tests.md
    test-design-review-report.md
  docs/
    end-to-end-usage.md
    input-output-contracts.md
    mvp-validation-checklist.md
```

## Sample Data and PoC Outputs

Sample inputs live outside the plugin:

- `samples/defects.sample.csv`
- `samples/test-design.sample.md`

PoC outputs:

- `prototype/output/defect-trend-report.md`
- `prototype/output/defect-pattern-catalog.yaml`
- `prototype/output/regression-viewpoint-catalog.md`
- `prototype/output/selected-regression-viewpoints.yaml`
- `prototype/output/missing-regression-tests.md`
- `prototype/output/test-design-review-report.md`

## Safety Boundary

This MVP is intentionally file-based:

- no hooks
- no MCP servers
- no network calls
- no automatic writes outside the requested output files
- no dependency on external CLIs

## Current Status

The MVP loop has been demonstrated on sample data. It is ready for a small real-data trial after defect data is masked and normalized.
