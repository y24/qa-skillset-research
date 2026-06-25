# Step 01: Load Patterns

## Goal

Read the defect pattern catalog and understand which patterns are eligible for regression viewpoint generation.

## Instructions

1. Load `defect-pattern-catalog.yaml`.
2. For each pattern, extract:
   - pattern ID
   - display name
   - status
   - risk level
   - confidence
   - defect IDs
   - affected areas
   - common conditions
   - recommended regression viewpoints
   - evidence records
3. Exclude retired patterns.
4. Keep monitor patterns if they are critical or have direct evidence.

## Output Fragment

Create an input summary:

| Pattern | Status | Risk | Confidence | Defects | Included | Reason |
|---|---|---|---|---|---|---|
