# Step 01: Input Check

## Goal

Understand the defect data and decide what can be analyzed safely.

## Instructions

1. Identify all input sources:
   - defect CSV/table
   - bug tickets
   - test failure logs
   - PR/fix summaries
   - incident reports
2. Count included and excluded records.
3. Map available fields to the analysis model:
   - id
   - title
   - description
   - feature/module
   - detected phase
   - severity
   - root cause
   - fix summary
   - related PR
   - test gap
4. List missing fields that affect analysis quality.
5. Do not infer trends yet.

## Output Fragment

Create an `Input Coverage` section containing:

- source name
- record count
- included/excluded count
- missing critical fields
- analysis limitations
