# Missing Regression Tests

## Assumptions

- Existing test case inventory: not provided
- Change context: not provided
- Therefore coverage is marked `unknown`, not confirmed `missing`.

## Coverage Gaps

| Viewpoint | Coverage | Gap | Suggested Test |
|---|---|---|---|
| RV-001 期首・期末境界での集計と比較 | unknown | 期首・期末・比較帳票の既存ケース有無が不明 | 期末日当日の明細を含む連結帳票、期首残高と当期初日仕訳の比較帳票 |
| RV-002 再実行・リトライ・部分失敗復旧の冪等性 | unknown | 再実行/リトライ/部分失敗復旧の既存ケース有無が不明 | 同一入力の2回実行、途中失敗後の再取込、大量CSVリトライ |
| RV-003 状態×権限×セッション更新のアクセス制御 | unknown | 状態×ロール×セッションの既存ケース有無が不明 | 承認済み仕訳の一般ユーザー編集不可、ロール変更直後の旧権限無効化 |
| RV-004 画面・帳票・Excel・会社別設定の金額一致 | unknown | 出力経路間の金額照合ケース有無が不明 | 画面/帳票/Excel/APIの税額一致、会社別丸め設定の外貨換算 |
| RV-005 ワークフローイベントごとの監査証跡 | unknown | 監査ログ検証ケース有無が不明 | 否認後再申請、承認、取消、権限違反操作の監査ログ必須項目確認 |
| RV-006 月末締め中の並行更新と残高照合 | unknown | 並行更新・排他制御・残高照合ケース有無が不明 | 締め処理中の同一勘定更新ブロック、処理後の残高テーブルと明細合計照合 |

## Unknown Coverage Checks

| Viewpoint | What To Check | Needed Source |
|---|---|---|
| RV-001 | 期間境界のテストケースが期首・期末を明示しているか | test case repository |
| RV-002 | 再実行、リトライ、部分失敗復旧を扱うケースがあるか | test case repository, automation suite |
| RV-003 | 権限マトリクスと状態遷移表を掛け合わせたケースがあるか | access control tests, workflow tests |
| RV-004 | 画面・帳票・Excel・APIの同一値照合があるか | report/export tests |
| RV-005 | 監査ログの必須項目を期待結果として確認しているか | audit log tests |
| RV-006 | 締め処理中の並行更新やロック待ちを再現するケースがあるか | batch/concurrency tests |
