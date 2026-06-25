# Masking Rules

Use these rules before running the MVP on real defect data.

## Goal

Keep enough detail for QA analysis while removing information that should not be placed in a prototype workspace or shared with an AI assistant.

## Remove Completely

Do not include:

- customer names
- company names outside your own generic organization label
- individual user names
- email addresses
- phone numbers
- addresses
- bank account numbers
- tax IDs
- contract IDs
- production database IDs
- real credentials, tokens, secrets, API keys
- screenshots or logs containing personal or customer data

## Replace With Stable Placeholders

| Sensitive Value | Replacement Example |
|---|---|
| customer name | `CUSTOMER_A`, `CUSTOMER_B` |
| user name | `USER_001`, `APPROVER_001` |
| company/account name | `COMPANY_A`, `ACCOUNT_001` |
| ticket ID if sensitive | `BUG-0001` |
| PR/change ID if sensitive | `PR-0001` |
| environment URL | `ENV_UAT`, `ENV_PROD` |
| file name containing customer | `IMPORT_FILE_A.csv` |

Keep placeholders stable within one dataset so related defects can still be clustered.

## Generalize But Preserve QA-Relevant Conditions

Preserve:

- date/period relation, such as fiscal year end or month end
- data volume category, such as 0 rows, 1 row, 10k rows
- role type, such as approver or general user
- workflow state, such as approved, rejected, resubmitted
- output channel, such as screen, report, Excel, API
- error class, such as validation error, timeout, duplicate registration

Mask:

- exact fiscal customer calendar if confidential
- exact monetary amounts if sensitive
- exact production timestamps if sensitive
- exact table/column names if internal security policy requires it

Examples:

| Before | After |
|---|---|
| `Acme Corpの2025/03/31連結売上が1,234,567円ずれる` | `CUSTOMER_Aのfiscal_year_end連結売上がmasked_amountずれる` |
| `tanaka@example.comが承認済み仕訳J-98765を編集できた` | `GENERAL_USER_001が承認済み仕訳JOURNAL_001を編集できた` |
| `s3://prod-customer-bucket/import/acme.csv` | `PROD_STORAGE/import/IMPORT_FILE_A.csv` |

## Keep Evidence Traceability

Even after masking, preserve stable source IDs:

- `BUG-0001`
- `PR-0001`
- `TC-0001`

This allows findings to trace back to the sanitized source without exposing original systems.

## Redaction Review Checklist

Before using real data:

- [ ] No customer names remain.
- [ ] No person names remain.
- [ ] No email addresses remain.
- [ ] No phone numbers remain.
- [ ] No tokens, secrets, or credentials remain.
- [ ] No production URLs remain.
- [ ] No raw logs with personal data remain.
- [ ] Stable masked IDs are used.
- [ ] QA-relevant conditions are still concrete.

## Do Not Over-Mask

Avoid replacing everything with vague text.

Bad:

```text
Bug happened in system.
```

Good:

```text
At fiscal_year_end, consolidated report excluded closing_day_transaction for COMPANY_A.
```

The second version is safe enough for analysis and still useful for test design.
