# Defect Trend Analysis Checklist

Use this checklist before finalizing outputs.

## Input Coverage

- [ ] Sources are listed.
- [ ] Included and excluded record counts are documented.
- [ ] Missing critical fields are named.
- [ ] Analysis limitations are explicit.

## Classification

- [ ] Defects are classified using the defect taxonomy.
- [ ] Unknown classifications are not forced.
- [ ] Severity and detected phase are preserved when available.
- [ ] Test gaps are marked as hypotheses unless directly supported.

## Evidence

- [ ] Every pattern has at least one evidence record.
- [ ] Evidence type is `direct`, `inferred`, or `claimed`.
- [ ] Root causes are not stated as confirmed without direct support.
- [ ] Open questions are listed for missing evidence.

## Patterns

- [ ] Pattern names describe failure conditions, not only modules.
- [ ] Pattern IDs are stable.
- [ ] Patterns include affected areas and common conditions.
- [ ] Risk levels are qualitative and justified.

## Outputs

- [ ] `defect-trend-report.md` is useful for human review.
- [ ] `defect-pattern-catalog.yaml` is structured for reuse.
- [ ] Recommendations trace to defect IDs or pattern IDs.
- [ ] Next actions are concrete enough for Phase4.
