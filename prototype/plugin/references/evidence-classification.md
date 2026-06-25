# Evidence Classification

Use this reference when writing QA findings, defect patterns, root-cause hypotheses, and regression recommendations.

## Evidence Types

| Type | Definition | Example |
|---|---|---|
| direct | Explicitly present in the source. | A bug ticket says totals were wrong at fiscal year end. |
| inferred | Derived from observed facts and reasoning. | Multiple period-end defects suggest missing period-boundary test design. |
| claimed | Stated by a person or proposed by analysis, but not verified in available sources. | "This probably affects exports too" without export evidence. |

## Confidence Labels

| Label | Use When |
|---|---|
| confirmed | Direct evidence is strong enough to treat as fact. |
| supported | Evidence supports the claim, but some detail may need follow-up. |
| hypothesis | Plausible and useful, but verification is required. |
| unknown | Available inputs do not support a conclusion. |

## Required Fields

```yaml
source_id: BUG-1234
source_type: defect_ticket
type: direct
observation: ""
reasoning: ""
needs_verification: false
```

## Rules

- Do not hide weak evidence behind confident wording.
- Use `hypothesis` when root cause is inferred.
- Use `unknown` instead of guessing missing phase, module, or cause.
- A recommendation must cite a pattern, defect, requirement, changed area, or review finding.
