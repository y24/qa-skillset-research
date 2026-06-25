# Test Design Review Report

## Review Summary

| Area | Result |
|---|---|
| Review target | `samples/test-design.sample.md` |
| Regression viewpoint input | `prototype/output/regression-viewpoint-catalog.md`, `prototype/output/selected-regression-viewpoints.yaml` |
| Overall result | Major gaps found |
| Critical findings | 2 |
| High findings | 4 |
| Medium findings | 1 |
| Main risk | The design covers happy paths but misses defect-derived regression conditions for authorization, concurrency, rerun, period boundaries, and auditability. |

## Review Context

| Input | Provided | Notes |
|---|---|---|
| Test design | Yes | `samples/test-design.sample.md` |
| Defect pattern catalog | Yes | `prototype/output/defect-pattern-catalog.yaml` |
| Regression viewpoint catalog | Yes | `prototype/output/regression-viewpoint-catalog.md` |
| Selected regression viewpoints | Yes | `prototype/output/selected-regression-viewpoints.yaml` |
| Requirement specification | No | Traceability to formal requirement IDs cannot be checked |
| Existing full test case inventory | No | Coverage outside the sample design is unknown |

## Current Test Design Coverage

| Area | Covered Conditions | Notes |
|---|---|---|
| Consolidated report | Normal April monthly total | Does not include fiscal year start/end or closing date boundary |
| Comparative report | Previous/current normal month display | Expected result is vague: "values are displayed" |
| CSV import | Happy path with 100 valid rows | No retry, rerun, partial failure, or large import scenario |
| Journal approval | Basic approver approval flow | No negative authorization or approved-state edit prevention |
| Excel export | File generation and row presence | No amount reconciliation or rounding oracle |
| Audit log | Approval operation appears | Does not cover reject/resubmit/cancel or required audit fields |
| Month-end close concurrency | Not covered | Explicitly out of scope |

## Regression Viewpoint Coverage

| Viewpoint | Coverage | Evidence | Gap |
|---|---|---|---|
| RV-001 期首・期末境界での集計と比較 | partial | TD-001 and TD-002 cover normal monthly reports only | fiscal_year_start, fiscal_year_end, opening_balance, closing_day_transaction are missing |
| RV-002 再実行・リトライ・部分失敗復旧の冪等性 | missing | TD-003 covers only valid 100-row import; out of scope excludes retrying failed import | rerun, retry_after_failure, partial_failure, large import are missing |
| RV-003 状態×権限×セッション更新のアクセス制御 | missing | TD-004 covers only successful approver approval; role matrix deferred | approved-state edit prevention, API negative path, role change/session invalidation are missing |
| RV-004 画面・帳票・Excel・会社別設定の金額一致 | partial | TD-005 confirms file exists and rows are present | screen/report/Excel/API amount reconciliation and rounding conditions are missing |
| RV-005 ワークフローイベントごとの監査証跡 | partial | TD-006 checks approval log exists | reject/resubmit/cancel, denied operations, and required audit fields are missing |
| RV-006 月末締め中の並行更新と残高照合 | missing | Out of scope explicitly excludes parallel user operations and month-end closing lock behavior | concurrent update, lock/wait/timeout, balance vs detail reconciliation are missing |

## Findings

### F-001: 状態×権限×セッション更新のアクセス制御が未設計

- Severity: Critical
- Category: missing_regression_viewpoint
- Related viewpoint: RV-003
- Evidence type: direct
- Source: `samples/test-design.sample.md`, TD-004 and Notes

#### Why It Matters

Phase5でCriticalに選定したRV-003は、承認済み仕訳の不正編集とロール変更後の旧権限利用を防ぐ観点である。現行設計は承認者の正常承認だけを確認しており、一般ユーザーの禁止操作、承認済み状態の編集不可、API直接更新、ロール変更直後のセッション無効化を確認していない。

#### Required Change

状態 `draft/submitted/approved/rejected/resubmitted` とロール `general_user/approver/admin`、アクセス経路 `UI/API`、セッション状態 `fresh_login/role_changed_active_session` を組み合わせたデシジョンテーブルを追加する。

### F-002: 月末締め中の並行更新と残高照合が明示的に除外されている

- Severity: Critical
- Category: missing_regression_viewpoint
- Related viewpoint: RV-006
- Evidence type: direct
- Source: `samples/test-design.sample.md`, Out of Scope

#### Why It Matters

RV-006は、締め処理中の並行更新で残高テーブルと明細が不一致になるCriticalリスクを扱う。サンプル設計では「Parallel user operations」と「Month-end closing lock behavior」がOut of Scopeになっており、最も重要なデータ整合性リスクがこのリリースの検証から外れている。

#### Required Change

少なくともrelease gateとして、締め処理中の同一勘定更新ブロック、ロック待ち/タイムアウト、処理後の残高テーブルと明細合計の照合を追加する。

### F-003: CSV取込の再実行・リトライ・部分失敗復旧が未設計

- Severity: High
- Category: missing_regression_viewpoint
- Related viewpoint: RV-002
- Evidence type: direct
- Source: `samples/test-design.sample.md`, TD-003 and Out of Scope

#### Why It Matters

RV-002は過去不具合BUG-008やBUG-012に基づく再発防止観点である。現行設計は100行の正常CSV取込だけで、リトライ後の重複、部分失敗後の再取込、同一入力の再実行を確認しない。

#### Required Change

同一CSVの2回実行、途中失敗後の再取込、1万行規模のリトライ、行単位ステータス確認を追加する。

### F-004: 期首・期末境界の集計と比較が不足している

- Severity: High
- Category: partial_regression_viewpoint
- Related viewpoint: RV-001
- Evidence type: direct
- Source: `samples/test-design.sample.md`, TD-001, TD-002, Notes

#### Why It Matters

TD-001とTD-002は通常月の帳票を確認しているが、RV-001で必要な期首・期末、期首残高、期末日当日明細、前期/当期/翌期比較を扱っていない。Notesにも「fiscal year boundary reports」の期待値未準備が明記されている。

#### Required Change

期末日当日の明細が集計に含まれるケース、期首残高と当期初日仕訳が二重計上されないケース、前期/当期/翌期の比較ケースを追加する。

### F-005: 監査ログ検証が承認イベントだけに限定されている

- Severity: High
- Category: partial_regression_viewpoint
- Related viewpoint: RV-005
- Evidence type: direct
- Source: `samples/test-design.sample.md`, TD-006

#### Why It Matters

RV-005は再申請、否認、取消、権限違反を含むワークフローイベントの監査証跡を求めている。現行設計は承認ログの存在確認だけで、監査ログの必須項目であるactor、timestamp、target_id、before/after、reasonも確認していない。

#### Required Change

否認後再申請、取消、権限違反操作の監査ログを追加し、必須項目を期待結果として明記する。

### F-006: Excel出力の金額一致性と丸めオラクルが不足している

- Severity: High
- Category: partial_regression_viewpoint
- Related viewpoint: RV-004
- Evidence type: direct
- Source: `samples/test-design.sample.md`, TD-005

#### Why It Matters

TD-005はExcelファイル生成と行存在のみを確認している。RV-004が求める画面・帳票・Excel・API間の税額一致、会社別丸め設定、外貨換算、小数境界の検証がないため、過去の1円差異や丸め設定漏れを再検出できない。

#### Required Change

画面表示、帳票、Excel、APIレスポンスの同一金額照合を追加し、小数第3位、負数、会社別丸め設定、外貨換算の期待値を明記する。

### F-007: 比較帳票の期待結果が曖昧

- Severity: Medium
- Category: missing_oracle
- Related viewpoint: RV-001
- Evidence type: direct
- Source: `samples/test-design.sample.md`, TD-002

#### Why It Matters

TD-002の期待結果は「values are displayed」であり、正しい比較値の定義がない。値が表示されても、期首残高の二重計上や対象期間誤りを検出できない。

#### Required Change

前期、当期、差額、期首残高、当期初日仕訳の期待値を具体的な数値または計算式で定義する。

## Recommended Test Additions

| Priority | Test Addition | Related Finding |
|---|---|---|
| Critical | 承認済み仕訳に対する一般ユーザーのUI/API編集禁止、ロール変更直後の旧権限無効化 | F-001 |
| Critical | 月末締め中の同一勘定更新ブロック、ロック待ち/タイムアウト、残高と明細合計の照合 | F-002 |
| High | CSV取込の同一入力2回実行、部分失敗後再取込、大量データリトライ | F-003 |
| High | 期末日当日明細、期首残高、当期初日仕訳、前期/当期/翌期比較 | F-004 |
| High | 否認後再申請、取消、権限違反操作の監査ログ必須項目確認 | F-005 |
| High | 画面・帳票・Excel・APIの金額照合、会社別丸め設定、外貨換算 | F-006 |
| Medium | 比較帳票の期待値を具体的な数値/計算式で定義 | F-007 |

## Open Questions

| Question | Impact |
|---|---|
| 正式な権限マトリクスと状態遷移表はあるか | RV-003の網羅条件を確定できる |
| 月末締め処理のロック仕様はブロック、待機、タイムアウトのどれか | RV-006の期待結果を確定できる |
| 帳票・Excel・APIで共通の金額計算仕様はあるか | RV-004のオラクルを定義できる |
| 監査ログの必須項目一覧はあるか | RV-005の期待結果を確定できる |
| 期首・期末データの期待値を作れるテストデータはあるか | RV-001の境界ケースを実装できる |
