# Evidence Model

Phase3 output: lightweight evidence model for QA analysis artifacts.

## Goal

Prevent QA Skill outputs from becoming plausible but unsupported analysis. Every finding, defect pattern, root-cause hypothesis, and regression recommendation should declare how it is supported.

## Evidence Types

| Type | Meaning | Allowed Use |
|---|---|---|
| `direct` | The source explicitly states or demonstrates the claim. | Defect title, reproduction step, observed failure, fix note, test result, code line, requirement text. |
| `inferred` | The claim is logically derived from observed facts. | Root cause hypothesis, likely missing test condition, cross-feature risk. |
| `claimed` | The claim is user-provided or model-proposed but not verified from available material. | Open question, assumption, suspected pattern requiring confirmation. |

## Confidence Labels

Use labels, not percentages.

| Label | Meaning |
|---|---|
| `confirmed` | Supported by direct evidence from multiple sources or a decisive source. |
| `supported` | Supported by at least one direct source or a strong inference chain. |
| `hypothesis` | Plausible but needs verification. |
| `unknown` | Not enough information. |

## Required Evidence Record

```yaml
evidence:
  - source_id: BUG-1234
    source_type: defect_ticket
    type: direct
    observation: "Expected total and actual total differed at fiscal year end."
    reasoning: "This directly demonstrates a period-boundary calculation failure."
    supports:
      - PAT-001
      - RV-001
    needs_verification: false
```

## Source Reference Rules

Use the most precise reference available.

| Source | Preferred Reference |
|---|---|
| defect ticket | `BUG-1234` plus field name if available |
| CSV row | source file + row ID |
| Markdown document | file path + heading |
| code | file path + line range |
| test result | run ID + test name |
| PR | PR ID + file or summary section |

## Reasoning Formula

Use this structure:

```text
{Observed fact} -> {quality implication} -> {recommended action}
```

Example:

```text
Three high-severity defects occurred at fiscal period boundaries -> period boundary handling is a recurring data integrity risk -> add regression viewpoints for period start/end and retrospective adjustment.
```

## Prohibited Patterns

- Do not use exact confidence percentages unless backed by a measured model or dataset.
- Do not say "likely" without explaining the evidence chain.
- Do not mix observed facts and hypotheses in the same sentence.
- Do not create a root cause as confirmed unless the source explicitly confirms it.
- Do not recommend regression tests without linking to a pattern, defect, requirement, or changed area.

## Finding Format

```yaml
finding:
  id: F-001
  title: Period-end aggregation tests are missing
  category: missing_regression_viewpoint
  severity: high
  confidence: supported
  evidence:
    - source_id: TEST-DESIGN-001
      type: direct
      observation: "Aggregation tests cover normal month only."
      reasoning: "The test design omits fiscal period start/end cases."
      needs_verification: false
    - source_id: PAT-001
      type: inferred
      observation: "Past defects cluster around fiscal period boundaries."
      reasoning: "A recurring defect pattern implies this omission is high risk."
      needs_verification: false
  recommendation:
    action: Add period start/end regression cases.
    target_artifact: test_design
```

## Pattern Status

| Status | Definition |
|---|---|
| `candidate` | Pattern appears possible but evidence is thin. |
| `active` | Pattern is supported and should influence reviews. |
| `retired` | Pattern is no longer relevant or has been superseded. |
| `monitor` | Pattern is not actionable yet but should be watched. |

## Verification Checklist

Before accepting an output:

- Every pattern has at least one evidence record.
- Every root-cause statement is either `direct` or marked as hypothesis.
- Every regression recommendation links to a pattern, defect ID, requirement, or changed area.
- Open questions are listed when input fields are missing.
- No finding relies only on generic QA best practice when defect-specific evidence is available.
