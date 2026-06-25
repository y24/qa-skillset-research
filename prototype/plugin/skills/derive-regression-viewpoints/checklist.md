# Derive Regression Viewpoints Checklist

## Input

- [ ] Defect pattern catalog was loaded.
- [ ] Retired patterns were excluded.
- [ ] Monitor patterns were handled deliberately.
- [ ] Missing optional inputs were listed.

## Prioritization

- [ ] Critical/high patterns are prioritized.
- [ ] Production/escaped evidence is considered when available.
- [ ] Data integrity, security, auditability, and financial correctness risks are weighted.
- [ ] The recommendation is not simply "run all regression".

## Viewpoints

- [ ] Each viewpoint has a stable ID.
- [ ] Each viewpoint cites pattern IDs and defect IDs.
- [ ] Test conditions are concrete.
- [ ] Expected outcomes or oracles are clear.
- [ ] Cadence is specified.

## Coverage

- [ ] Existing coverage is marked covered/partial/missing only when evidence exists.
- [ ] Unknown coverage is marked as unknown when test cases are unavailable.
- [ ] Missing-regression-tests contains actionable suggested tests.

## Outputs

- [ ] `regression-viewpoint-catalog.md` is readable.
- [ ] `selected-regression-viewpoints.yaml` is structured for reuse.
- [ ] `missing-regression-tests.md` separates missing from unknown.
