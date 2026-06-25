# Defect Trend Report

## Summary

- 対象期間: サンプルデータのため未指定
- 入力件数: 12
- 分析対象: 12
- 分析対象外: 0
- 主な傾向: 期間境界、再実行/冪等性、権限×状態、監査証跡、出力一致性に再利用可能なパターンが見られる
- 重要リスク: 期末・期首の金額誤り、承認済みデータの不正更新、月末締め中の残高不整合

## Input Coverage

| Source | Count | Included | Excluded | Missing Fields | Notes |
|---|---:|---:|---:|---|---|
| `samples/defects.sample.csv` | 12 | 12 | 0 | 発生日、改修日、本番流出有無の詳細、影響顧客数 | PoC用サンプル。全行にid/title/description/feature/detected_phase/severity/root_cause/fix_summary/test_gapあり |

## Classification Summary

| Category | Count | Representative Defects | Notes |
|---|---:|---|---|
| period_boundary_error | 2 | BUG-001, BUG-002 | 期末日、期首切替で金額誤り |
| idempotency_error | 3 | BUG-003, BUG-008, BUG-012 | 再集計、リトライ、部分失敗後の再取込 |
| authorization_error | 2 | BUG-004, BUG-010 | 承認済み状態とロール変更後セッション |
| auditability | 1 | BUG-005 | 再申請操作の監査ログ欠落 |
| reporting_error | 3 | BUG-001, BUG-006, BUG-007 | 帳票合計、Excel差異、0件帳票 |
| calculation_error | 2 | BUG-006, BUG-011 | 税額丸め、外貨換算丸め |
| concurrency_error | 1 | BUG-009 | 月末締め中の並行更新 |
| data_integrity_error | 2 | BUG-008, BUG-009 | 重複登録、残高不整合 |

## Top Defect Patterns

| Rank | Pattern | Count | Severity Mix | Affected Areas | Evidence Level | Recommended Action |
|---:|---|---:|---|---|---|---|
| 1 | PAT-001 Period boundary financial aggregation | 2 | High: 2 | 連結帳票, 比較帳票 | direct | 期首・期末を明示した回帰観点を追加 |
| 2 | PAT-002 Rerun and retry idempotency failure | 3 | High: 2, Medium: 1 | 再集計, CSV取込 | direct | 再実行、リトライ、部分失敗復旧の観点を横展開 |
| 3 | PAT-003 Authorization and state transition mismatch | 2 | Critical: 1, High: 1 | 仕訳承認, 権限管理 | direct | 状態×ロール×セッション更新のデシジョンテーブル化 |
| 4 | PAT-004 Output reconciliation and rounding inconsistency | 2 | Medium: 2 | Excel出力, 外貨換算 | direct | 画面・帳票・Excel・会社別設定の照合観点を追加 |
| 5 | PAT-005 Audit trail gap in workflow events | 1 | High: 1 | 監査ログ | direct | 承認・否認・再申請・再編集の監査イベント確認を追加 |
| 6 | PAT-006 Concurrent month-end processing data inconsistency | 1 | Critical: 1 | 月末締め | direct | 締め処理中の並行更新と排他制御のテストを追加 |

## Pattern Details

### PAT-001: Period Boundary Financial Aggregation

#### Description

期末日・期首切替など、会計期間の境界で金額の集計対象や比較対象が誤るパターン。金額誤りとして影響が大きく、帳票・比較・連結処理に横展開しやすい。

#### Evidence

| Source | Type | Observation | Reasoning |
|---|---|---|---|
| BUG-001 | direct | 期末日当日の売上明細が連結帳票の合計に含まれない | 期末日の境界条件が直接の不具合条件になっている |
| BUG-002 | direct | 前期比較帳票で期首残高と当期初日の仕訳が重複表示される | 期首切替の扱いが比較帳票で誤っている |

#### Common Conditions

- fiscal_year_start
- fiscal_year_end
- previous/current period comparison
- closing date included/excluded
- opening balance plus first-day transaction

#### Root Cause Hypotheses

| Hypothesis | Evidence Type | Reasoning | Verification Needed |
|---|---|---|---|
| 期間境界の期待仕様とテスト条件が不足していた | inferred | BUG-001とBUG-002のtest_gapがともに期首/期末条件不足を示す | 過去テストケースで期首・期末が明示されていたか確認 |

#### Regression Candidates

- 期末日当日を含む/含まない条件の集計確認
- 期首残高と当期初日仕訳の重複排除確認
- 前期・当期・翌期の比較帳票確認
- 0件、1件、複数件の境界データ確認

### PAT-002: Rerun and Retry Idempotency Failure

#### Description

再集計、CSV取込、リトライ、部分失敗後の再実行で、古い結果が残る、重複登録される、正常行が誤ってスキップされるパターン。

#### Evidence

| Source | Type | Observation | Reasoning |
|---|---|---|---|
| BUG-003 | direct | 遡及修正後に再集計しても一部明細が旧金額のまま残る | 再実行時のキャッシュ無効化が品質リスク |
| BUG-008 | direct | 1万行超CSV取込でリトライ後に一部行が重複登録される | リトライ時の冪等性不足が直接示されている |
| BUG-012 | direct | 失敗後の再取込で正常行も取込済み扱いになる | 部分失敗後の状態管理不足が再実行リスクになる |

#### Common Conditions

- rerun
- retry_after_failure
- retrospective_adjustment
- partial_failure
- large_import
- stale_cache

#### Root Cause Hypotheses

| Hypothesis | Evidence Type | Reasoning | Verification Needed |
|---|---|---|---|
| 再実行時の状態初期化・一意性・行単位ステータスの設計が弱い | inferred | fix_summaryがキャッシュ破棄、一意キー、行単位ステータスに集中している | 実装設計と既存テストのセットアップ/後始末を確認 |

#### Regression Candidates

- 同一入力を2回実行して結果が変わらないこと
- 途中失敗後に再実行して成功行・失敗行が正しく扱われること
- 遡及修正後に旧値が残らないこと
- 大量データ取込で重複登録が起きないこと

### PAT-003: Authorization and State Transition Mismatch

#### Description

承認済み、否認、ロール変更後など、状態と権限が交差する条件で不正操作が可能になるパターン。

#### Evidence

| Source | Type | Observation | Reasoning |
|---|---|---|---|
| BUG-004 | direct | 承認済み仕訳が一般ユーザーでも編集可能 | 状態と権限の組合せチェック不足が直接示されている |
| BUG-010 | direct | ロール変更直後も旧権限で承認可能 | セッション/権限キャッシュが権限判定に影響している |

#### Common Conditions

- approved_state
- role_change
- stale_permission_cache
- session_continuation
- denied_to_resubmitted

#### Root Cause Hypotheses

| Hypothesis | Evidence Type | Reasoning | Verification Needed |
|---|---|---|---|
| 状態遷移表と権限マトリクスを組み合わせたテストが不足していた | inferred | test_gapが状態×権限、権限変更後セッションを示す | 既存テストにロール変更直後・承認済み編集不可の組合せがあるか確認 |

#### Regression Candidates

- ステータス別に編集/承認/否認/再申請の可否を確認
- ロール変更直後の既存セッションで旧権限が使えないことを確認
- API直接呼び出しでも権限が強制されることを確認

### PAT-004: Output Reconciliation and Rounding Inconsistency

#### Description

画面、帳票、Excel出力、会社別設定、通貨換算など、同じ値が複数の表示・出力経路で一致しないパターン。

#### Evidence

| Source | Type | Observation | Reasoning |
|---|---|---|---|
| BUG-006 | direct | 画面上の税額とExcel出力後の税額が1円ずれる | 出力経路間の丸め規則不一致が直接示されている |
| BUG-011 | direct | 会社別丸め方式が外貨換算処理で反映されない | 設定参照漏れが計算結果の不一致につながっている |

#### Common Conditions

- decimal_rounding
- currency_conversion
- company_setting
- screen_report_export_reconciliation

#### Root Cause Hypotheses

| Hypothesis | Evidence Type | Reasoning | Verification Needed |
|---|---|---|---|
| 画面・出力・換算処理で共通の計算オラクルを使うテストが不足していた | inferred | fix_summaryが共通丸め関数と会社別設定参照に集中している | テストで画面、帳票、Excel、API値を照合しているか確認 |

#### Regression Candidates

- 画面表示、帳票、Excel出力の金額一致確認
- 会社別丸め設定ごとの外貨換算確認
- 小数第3位、0.5境界、負数、複数通貨の丸め確認

### PAT-005: Audit Trail Gap in Workflow Events

#### Description

業務イベントは成功しているが、監査ログに必要な操作証跡が残らないパターン。会計・承認系では不具合の直接影響が画面に出ないため、明示的な監査証跡テストが必要。

#### Evidence

| Source | Type | Observation | Reasoning |
|---|---|---|---|
| BUG-005 | direct | 否認後に再申請した仕訳の監査ログが欠落する | 再申請イベントの監査証跡が直接欠落している |

#### Common Conditions

- denied_to_resubmitted
- audit_event
- workflow_transition

#### Root Cause Hypotheses

| Hypothesis | Evidence Type | Reasoning | Verification Needed |
|---|---|---|---|
| 通常の画面動作確認では監査ログの期待結果が確認されていなかった | inferred | test_gapが監査証跡の期待結果確認不足を示す | 既存テストで監査ログを検証対象にしているか確認 |

#### Regression Candidates

- 作成、編集、承認、否認、再申請、取消の監査ログ確認
- 操作者、時刻、対象ID、変更前後、理由の記録確認
- 権限違反操作が監査ログに残るか確認

### PAT-006: Concurrent Month-End Processing Data Inconsistency

#### Description

月末締めなどの重要バッチ/業務処理中に、別ユーザー更新が重なって残高や明細の整合性が崩れるパターン。サンプル上は単発だがCriticalかつ会計データの信頼性に直結するためmonitor対象とする。

#### Evidence

| Source | Type | Observation | Reasoning |
|---|---|---|---|
| BUG-009 | direct | 締め処理中の別ユーザー更新で残高テーブルと明細が不一致 | 並行更新によるデータ整合性破壊が直接示されている |

#### Common Conditions

- month_end
- concurrent_update
- locking
- balance_reconciliation

#### Root Cause Hypotheses

| Hypothesis | Evidence Type | Reasoning | Verification Needed |
|---|---|---|---|
| 締め処理中の排他制御と並行更新テストが不足していた | inferred | root_causeが排他制御不足、test_gapが並行更新テスト不足を示す | 対象処理のロック範囲と再試行設計を確認 |

#### Regression Candidates

- 締め処理中に同一勘定を更新できないこと
- ロック待ち・タイムアウト時のメッセージ確認
- 処理後に残高テーブルと明細合計が一致すること

## Cross-Cutting Risks

| Risk | Related Patterns | Impact | Suggested Gate |
|---|---|---|---|
| 期間境界での金額誤り | PAT-001, PAT-004 | 財務帳票や比較帳票の誤表示 | 期首・期末・丸め境界の回帰観点を必須化 |
| 再実行で結果が変わる | PAT-002, PAT-006 | 重複、旧値残存、整合性破壊 | 再実行・リトライ・並行更新の品質ゲート追加 |
| 権限と監査の抜け漏れ | PAT-003, PAT-005 | 不正更新、監査証跡不足 | 状態×権限×監査ログのレビュー観点追加 |

## Recommended Next Actions

1. `derive-regression-viewpoints` のPoCでは PAT-001、PAT-002、PAT-003 を優先して回帰観点化する。
2. `test-design-review` のチェックリストに、期間境界、再実行、状態×権限、監査ログ、出力一致性を追加する。
3. 実データで使う前に、defect CSVへ `found_date`, `release`, `escaped`, `customer_impact`, `component`, `existing_test_case_id` を追加する。

## Open Questions

| Question | Why It Matters | Needed Source |
|---|---|---|
| どの不具合が本番流出か | regression priorityを上げる根拠になる | incident/release data |
| 既存テストケースIDとの紐づきはあるか | 既存coverage gapを特定できる | test management data |
| 改修後に追加された回帰テストはあるか | 再発防止済みか判断できる | PR/test diff |
| 顧客影響や金額影響の大きさはどれくらいか | risk_levelの精度が上がる | support/incident data |
