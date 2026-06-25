# Step 04: Root Cause Hypotheses

## Goal

Identify root-cause hypotheses without overstating unsupported conclusions.

## Instructions

1. Use explicit root cause fields when available.
2. Otherwise derive hypotheses from:
   - repeated trigger conditions
   - missing test gap fields
   - fix summaries
   - escaped phase
   - affected modules
3. Separate:
   - confirmed root causes
   - inferred root causes
   - open hypotheses
4. For each hypothesis, explain what evidence would confirm or reject it.

## RCA Prompts

Use these when helpful:

- What condition allowed the defect to appear?
- What test or review should have caught it?
- Was the miss caused by requirements ambiguity, data setup, environment, implementation, or review gap?
- Is the same failure condition likely in other modules?

## Output Fragment

Create `Root Cause Hypotheses` and `Open Questions` sections.
