# Test Design: Closing and Reporting Release

## Scope

This test design covers month-end closing, consolidated reporting, CSV import, journal approval, and Excel export for the next accounting release.

## Test Areas

### TD-001: Consolidated Report Normal Monthly Total

- Feature: 連結帳票
- Preconditions: normal monthly sales data exists for April
- Steps:
  1. Run consolidated report for April.
  2. Compare total amount with prepared expected total.
- Expected result: report total equals expected total.
- Priority: High

### TD-002: Comparative Report Normal Month

- Feature: 比較帳票
- Preconditions: previous month and current month data exist.
- Steps:
  1. Run comparative report.
  2. Confirm previous and current month totals are shown.
- Expected result: values are displayed.
- Priority: Medium

### TD-003: CSV Import Happy Path

- Feature: CSV取込
- Preconditions: valid CSV with 100 rows.
- Steps:
  1. Import the CSV.
  2. Confirm all rows are registered.
- Expected result: import succeeds with 100 rows.
- Priority: High

### TD-004: Journal Approval Basic Flow

- Feature: 仕訳承認
- Preconditions: user with approver role and submitted journal.
- Steps:
  1. Approver opens submitted journal.
  2. Approver approves journal.
  3. General user confirms status is approved.
- Expected result: status becomes approved.
- Priority: High

### TD-005: Excel Export Basic Check

- Feature: Excel出力
- Preconditions: report data exists.
- Steps:
  1. Export report to Excel.
  2. Open the file.
  3. Confirm rows are present.
- Expected result: Excel file is generated and contains report rows.
- Priority: Medium

### TD-006: Audit Log for Approval

- Feature: 監査ログ
- Preconditions: submitted journal exists.
- Steps:
  1. Approve journal.
  2. Open audit log screen.
  3. Confirm approval operation is shown.
- Expected result: approval log exists.
- Priority: Medium

## Out of Scope

- Performance tests
- Parallel user operations
- API direct authorization tests
- Month-end closing lock behavior
- Retrying failed import

## Notes

- Detailed role matrix will be tested in a later cycle.
- The expected values for fiscal year boundary reports are not yet prepared.
