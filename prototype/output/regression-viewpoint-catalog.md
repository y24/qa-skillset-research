# Regression Viewpoint Catalog

## Summary

| Viewpoint | Priority | Source Patterns | Cadence | Coverage |
|---|---|---|---|---|
| RV-001 期首・期末境界での集計と比較 | High | PAT-001 | release_gate, change_triggered | unknown |
| RV-002 再実行・リトライ・部分失敗復旧の冪等性 | High | PAT-002 | release_gate, change_triggered, nightly_or_scheduled | unknown |
| RV-003 状態×権限×セッション更新のアクセス制御 | Critical | PAT-003 | release_gate, smoke_if_changed | unknown |
| RV-004 画面・帳票・Excel・会社別設定の金額一致 | Medium | PAT-004 | change_triggered | unknown |
| RV-005 ワークフローイベントごとの監査証跡 | High | PAT-005 | release_gate, exploratory_charter | unknown |
| RV-006 月末締め中の並行更新と残高照合 | Critical | PAT-006 | release_gate, monitor | unknown |

## Input Summary

| Pattern | Status | Risk | Confidence | Defects | Included | Reason |
|---|---|---|---|---|---|---|
| PAT-001 | active | high | supported | BUG-001, BUG-002 | Yes | 金額誤りにつながる期間境界不具合が複数ある |
| PAT-002 | active | high | supported | BUG-003, BUG-008, BUG-012 | Yes | 再実行/リトライ系の不具合が3件あり、本番検出も含む |
| PAT-003 | active | critical | supported | BUG-004, BUG-010 | Yes | 権限・承認操作のCriticalリスク |
| PAT-004 | active | medium | supported | BUG-006, BUG-011 | Yes | 金額一致性に関わるためmediumでも回帰観点化する |
| PAT-005 | active | high | supported | BUG-005 | Yes | 監査証跡は通常UI確認で漏れやすい |
| PAT-006 | monitor | critical | supported | BUG-009 | Yes | 単発だがCriticalかつ月末締めのデータ整合性に直結する |

## Prioritization

| Rank | Pattern | Priority | Why | Recommended Cadence |
|---:|---|---|---|---|
| 1 | PAT-003 | Critical | 承認済みデータの不正編集、ロール変更後の旧権限利用がある | release_gate, smoke_if_changed |
| 2 | PAT-006 | Critical | 月末締め中の並行更新で残高と明細が不一致になる | release_gate, monitor |
| 3 | PAT-002 | High | 再実行/リトライ/部分失敗復旧の不具合が3件あり、本番検出を含む | release_gate, change_triggered |
| 4 | PAT-001 | High | 期首・期末の金額誤りが複数ある | release_gate, change_triggered |
| 5 | PAT-005 | High | 監査ログ欠落は業務上の証跡不足につながる | release_gate, exploratory_charter |
| 6 | PAT-004 | Medium | 金額丸めと出力一致性の不具合が複数ある | change_triggered |

## Viewpoints

### RV-001: 期首・期末境界での集計と比較

#### Priority

High

#### Rationale

- Source patterns: PAT-001
- Source defects: BUG-001, BUG-002
- Business impact: 会計期間境界で財務帳票や比較帳票の金額が誤ると、レビュー・承認・外部報告の判断を誤る。

#### Applicable Areas

- 連結帳票
- 比較帳票
- 帳票出力
- 仕訳生成
- Excel出力

#### Test Conditions

| Condition | Values |
|---|---|
| period position | fiscal_year_start, fiscal_year_end, normal_day |
| data volume | no_data, single_record, multiple_records |
| comparison target | previous_period, current_period, next_period |
| transaction timing | opening_balance, first_day_transaction, closing_day_transaction |
| adjustment | none, retrospective_adjustment |

#### Suggested Test Viewpoints

- 期末日当日の明細が集計に含まれること。
- 期首残高と当期初日仕訳が二重計上されないこと。
- 前期・当期・翌期比較で対象期間が正しく切り替わること。
- 0件、1件、複数件のデータで期待値が明示されること。

#### Expected Outcome / Oracle

- 明細合計、帳票合計、比較値が同一の期間定義に基づいて一致する。
- 期首残高と当期取引は重複しない。

#### Cadence

- release_gate
- change_triggered when aggregation, reporting, period logic, or closing process changes

#### Evidence

| Source | Type | Reasoning |
|---|---|---|
| PAT-001 | inferred | 複数のHigh不具合が期首・期末境界で発生している |
| BUG-001 | direct | 期末日当日の売上明細が連結帳票の合計に含まれなかった |
| BUG-002 | direct | 期首残高と当期初日の仕訳が重複表示された |

### RV-002: 再実行・リトライ・部分失敗復旧の冪等性

#### Priority

High

#### Rationale

- Source patterns: PAT-002
- Source defects: BUG-003, BUG-008, BUG-012
- Business impact: 再実行で旧値、重複、誤スキップが起きると、修正作業やバッチ復旧でデータがさらに壊れる。

#### Applicable Areas

- 再集計
- CSV取込
- バッチ処理
- 外部連携取込
- リカバリ処理

#### Test Conditions

| Condition | Values |
|---|---|
| execution | first_run, rerun, retry_after_failure |
| failure mode | full_success, partial_failure, timeout, validation_error |
| data volume | small, large |
| state | no_previous_result, existing_result, stale_cache |
| correction | none, retrospective_adjustment |

#### Suggested Test Viewpoints

- 同じ入力を複数回実行しても結果が変わらないこと。
- 途中失敗後に再実行して成功行・失敗行が正しく扱われること。
- リトライ後に重複登録されないこと。
- 遡及修正後に旧値が残らないこと。

#### Expected Outcome / Oracle

- 再実行後の最終状態は、成功した1回実行の結果と同じになる。
- 失敗行と成功行の状態が行単位で追跡できる。

#### Cadence

- release_gate
- change_triggered when import, aggregation, retry, cache, or recovery logic changes
- nightly_or_scheduled for expensive large-data scenarios

#### Evidence

| Source | Type | Reasoning |
|---|---|---|
| PAT-002 | inferred | 再実行/リトライ/部分失敗復旧に共通する不具合が3件ある |
| BUG-003 | direct | 再集計後に旧金額が残った |
| BUG-008 | direct | リトライ後にCSV行が重複登録された |
| BUG-012 | direct | 再取込時に正常行も取込済み扱いになった |

### RV-003: 状態×権限×セッション更新のアクセス制御

#### Priority

Critical

#### Rationale

- Source patterns: PAT-003
- Source defects: BUG-004, BUG-010
- Business impact: 承認済み仕訳の不正編集や旧権限での承認は、職務分掌と監査可能性を損なう。

#### Applicable Areas

- 仕訳承認
- 権限管理
- ユーザー管理
- 承認ワークフロー
- API更新処理

#### Test Conditions

| Condition | Values |
|---|---|
| workflow state | draft, submitted, approved, rejected, resubmitted |
| role | general_user, approver, admin |
| session state | fresh_login, active_session_before_role_change, expired_session |
| access path | UI, API |
| operation | view, edit, approve, reject, resubmit, delete |

#### Suggested Test Viewpoints

- 承認済み仕訳は一般ユーザーがUI/APIどちらでも編集できないこと。
- ロール変更後、既存セッションで旧権限の承認操作ができないこと。
- 状態遷移ごとに許可/禁止操作が権限マトリクスと一致すること。
- 禁止操作は監査ログまたはセキュリティログに残ること。

#### Expected Outcome / Oracle

- 状態とロールの組合せごとに、許可された操作だけが成功する。
- 権限変更は既存セッションに即時または設計どおり反映される。

#### Cadence

- release_gate
- smoke_if_changed when auth, workflow, session, or approval logic changes

#### Evidence

| Source | Type | Reasoning |
|---|---|---|
| PAT-003 | inferred | 状態と権限が交差する条件でCritical/High不具合が発生している |
| BUG-004 | direct | 承認済み仕訳が一般ユーザーでも編集可能だった |
| BUG-010 | direct | ロール変更直後も旧権限で承認できた |

### RV-004: 画面・帳票・Excel・会社別設定の金額一致

#### Priority

Medium

#### Rationale

- Source patterns: PAT-004
- Source defects: BUG-006, BUG-011
- Business impact: 同じ金額が表示経路や会社別設定でずれると、利用者がどの値を正とすべきか判断できない。

#### Applicable Areas

- Excel出力
- 外貨換算
- 帳票出力
- 画面表示
- APIレスポンス

#### Test Conditions

| Condition | Values |
|---|---|
| output channel | screen, report, Excel, API |
| rounding mode | round_half_up, floor, ceiling, company_specific |
| decimal boundary | 0.004, 0.005, 0.006, negative_amount |
| currency | base_currency, foreign_currency |
| company setting | default, overridden |

#### Suggested Test Viewpoints

- 画面、帳票、Excel、APIで同一金額が一致すること。
- 会社別丸め設定が外貨換算に反映されること。
- 小数第3位、負数、複数通貨で丸め結果が期待値と一致すること。

#### Expected Outcome / Oracle

- 全出力経路が同じ丸め規則と会社別設定を使う。
- 期待値は共通の計算仕様または独立算出したテストオラクルと一致する。

#### Cadence

- change_triggered when calculation, rounding, export, report, or company setting logic changes

#### Evidence

| Source | Type | Reasoning |
|---|---|---|
| PAT-004 | inferred | 丸め・設定参照・出力経路不一致の不具合が複数ある |
| BUG-006 | direct | 画面税額とExcel税額が1円ずれた |
| BUG-011 | direct | 会社別丸め方式が外貨換算処理で反映されなかった |

### RV-005: ワークフローイベントごとの監査証跡

#### Priority

High

#### Rationale

- Source patterns: PAT-005
- Source defects: BUG-005
- Business impact: 操作自体が成功しても監査ログが欠落すると、後続調査や監査で正当性を説明できない。

#### Applicable Areas

- 監査ログ
- 仕訳承認
- ワークフロー
- 権限違反操作

#### Test Conditions

| Condition | Values |
|---|---|
| workflow event | create, edit, submit, approve, reject, resubmit, cancel |
| actor | general_user, approver, admin |
| outcome | success, denied, validation_error |
| audit fields | actor, timestamp, target_id, before_after, reason |

#### Suggested Test Viewpoints

- 否認後の再申請で監査ログが出力されること。
- 承認、否認、再申請、取消の各イベントで必要項目が残ること。
- 権限違反操作が適切に記録されること。

#### Expected Outcome / Oracle

- 重要な業務イベントごとに監査ログが1件以上作成され、必須項目が欠落しない。

#### Cadence

- release_gate
- exploratory_charter for workflow-heavy releases
- change_triggered when workflow or audit logging changes

#### Evidence

| Source | Type | Reasoning |
|---|---|---|
| PAT-005 | inferred | 監査ログは通常画面確認だけでは欠落を見逃しやすい |
| BUG-005 | direct | 否認後に再申請した仕訳の監査ログが欠落した |

### RV-006: 月末締め中の並行更新と残高照合

#### Priority

Critical

#### Rationale

- Source patterns: PAT-006
- Source defects: BUG-009
- Business impact: 締め処理中の不整合は、財務データの信頼性と復旧作業に直接影響する。

#### Applicable Areas

- 月末締め
- 残高集計
- 明細更新
- 排他制御
- リカバリ処理

#### Test Conditions

| Condition | Values |
|---|---|
| process state | before_close, during_close, after_close |
| concurrent operation | same_account_update, different_account_update, approval, import |
| lock behavior | block, wait, timeout, retry |
| reconciliation | balance_vs_detail, report_vs_table |

#### Suggested Test Viewpoints

- 締め処理中に同一勘定を更新できないこと。
- ロック待ちまたはタイムアウト時に利用者へ明確な結果が返ること。
- 締め処理後に残高テーブルと明細合計が一致すること。
- 失敗・中断後に再実行しても整合性が保たれること。

#### Expected Outcome / Oracle

- 同時更新によって残高と明細の不一致が発生しない。
- 競合時の動作が仕様どおりで、復旧後も照合が通る。

#### Cadence

- release_gate
- monitor when closing, locking, balance, import, or batch logic changes

#### Evidence

| Source | Type | Reasoning |
|---|---|---|
| PAT-006 | inferred | 単発だがCriticalで財務データ整合性に直結する |
| BUG-009 | direct | 締め処理中の別ユーザー更新で残高テーブルと明細が不一致になった |

## Coverage Matrix

No existing test case inventory was provided in Phase5, so coverage is marked as `unknown`.

| Viewpoint | Coverage | Evidence | Gap |
|---|---|---|---|
| RV-001 | unknown | Existing tests not provided | 期首・期末・比較帳票の既存ケース有無を確認する |
| RV-002 | unknown | Existing tests not provided | 再実行、リトライ、部分失敗復旧の既存ケース有無を確認する |
| RV-003 | unknown | Existing tests not provided | 状態×権限×セッションの既存ケース有無を確認する |
| RV-004 | unknown | Existing tests not provided | 出力経路間の金額照合ケース有無を確認する |
| RV-005 | unknown | Existing tests not provided | 監査ログ検証ケース有無を確認する |
| RV-006 | unknown | Existing tests not provided | 並行更新・排他制御・残高照合ケース有無を確認する |
