# Step 03: Find Patterns

## Goal

Group classified defects into recurring patterns that can drive regression viewpoints and review checklists.

## Instructions

1. Cluster defects by shared failure condition, not only by component.
2. Prefer patterns that are reusable as test viewpoints.
3. For each pattern, record:
   - pattern ID
   - display name
   - summary
   - related defect IDs
   - affected areas
   - common conditions
   - quality characteristics
   - evidence records
   - risk level
   - confidence label
4. Use `candidate` status when evidence is thin.

## Pattern Criteria

A good pattern is:

- specific enough to test
- repeated or high impact
- linked to source defects
- expressible as a future review or regression viewpoint

## Output Fragment

Create `Top Defect Patterns` and `Pattern Details` sections.
