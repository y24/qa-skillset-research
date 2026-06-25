# Next Steps

Phase8までで、サンプルデータを使ったMVPの中核ループは一通り動いた。

```text
defect-trend-analysis
  -> derive-regression-viewpoints
  -> test-design-review
```

ここから先は、実データで価値を確認してから、個人用Pluginとして整える。

## Phase9: 実データ50-100件で試行

目的:

- サンプルではなく、実際の不具合データでMVPが使えるか確認する。

入力:

- `samples/real-defects.template.csv` を元にしたサニタイズ済みCSV
- 可能なら既存テストケース一覧
- 可能ならレビュー対象の実テスト設計書

作業:

1. 実不具合50-100件をエクスポートする。
2. `prototype/plugin/docs/masking-rules.md` に従ってマスキングする。
3. `prototype/plugin/docs/real-data-trial-checklist.md` で投入前確認をする。
4. `defect-trend-analysis` を実行する。
5. 出てきた defect pattern を人間がレビューする。
6. `derive-regression-viewpoints` を実行する。
7. `test-design-review` を実テスト設計書に適用する。

成果物:

- real-data版 `defect-trend-report.md`
- real-data版 `defect-pattern-catalog.yaml`
- real-data版 `regression-viewpoint-catalog.md`
- real-data版 `test-design-review-report.md`
- 実データ試行レビュー

評価観点:

- パターン分類がQA/業務担当から見て妥当か
- 回帰観点が実際にテストケース化できるか
- レビュー指摘が具体的で、既存レビューに足せるか
- 根拠が追えるか
- 推測が混ざりすぎていないか

## Phase10: 実データ結果を反映してMVPを調整

目的:

- 実データ試行で見つかった弱点を修正する。

作業:

1. `defect-taxonomy.md` に実業務で出た分類を追加する。
2. `regression-selection-criteria.md` の優先度判断を調整する。
3. `test-design-review-checklist.md` に実レビューで使う観点を追加する。
4. 出力テンプレートの過不足を直す。
5. 不要に細かいパターンを統合するルールを追加する。

判断基準:

- 実データで繰り返し使える分類になっているか
- 出力が長すぎず、レビューに使える密度か
- Critical/Highの指摘が本当に優先すべきものになっているか

## Phase11: 個人用Pluginとして整える

目的:

- prototypeから、自分が日常的に呼べるPlugin/Skillsetへ整える。

作業:

1. `prototype/plugin` を正式なplugin作業場所へコピーする。
2. READMEを実運用向けに書き直す。
3. サンプルデータと実データを分離する。
4. 出力先の推奨ディレクトリを決める。
5. Skill名、説明、triggerを見直す。
6. 最小限の使い方をREADME先頭に置く。

候補構成:

```text
qa-personal-skillset/
  .claude-plugin/
    plugin.json
  skills/
    defect-trend-analysis/
    derive-regression-viewpoints/
    test-design-review/
  references/
  templates/
  docs/
  samples/
```

注意:

- hooks、MCP、外部CLIはまだ入れない。
- まずは手動起動の安全なSkillsetとして運用する。

## Phase12: `functional-test-design` を追加

目的:

- 不具合由来の回帰観点を、通常のテスト設計生成にも使えるようにする。

入力:

- 要件、仕様、チケット
- `regression-viewpoint-catalog.md`
- `defect-pattern-catalog.yaml`

出力:

- test conditions
- test cases
- boundary values
- decision tables
- state transition cases
- defect-derived regression cases

採用する技法:

- 同値分割
- 境界値分析
- デシジョンテーブル
- 状態遷移
- ユースケース
- エラー推測
- 過去不具合由来の回帰観点

## Phase13: `qa-intake` を追加

目的:

- 入力資料を見て、どのSkillを使うべきか振り分ける入口を作る。

役割:

- 入力が不具合一覧なら `defect-trend-analysis`
- 入力がdefect patternなら `derive-regression-viewpoints`
- 入力がテスト設計書なら `test-design-review`
- 入力が仕様/要件なら `functional-test-design`

出力:

- 推奨Skill
- 不足情報
- 想定リスク
- 次に作るべき成果物

## Phase14: `nfr-code-review` を追加

目的:

- 非機能・コードレビュー観点をQA Skillsetに追加する。

対象観点:

- performance
- reliability
- security
- maintainability
- operability
- accessibility
- localization
- data integrity
- auditability

参考:

- `Iron-Ham/claude-deep-review`
- `trailofbits/skills`
- `claude-plugins-official/security-guidance`

注意:

- MVPに混ぜず、独立Skillとして作る。
- セキュリティ専門領域は自前で抱え込みすぎず、専門Skill参照に寄せる。

## Phase15: 運用化

目的:

- 一度作って終わりではなく、不具合とレビュー観点を継続更新する。

運用案:

1. 月1回、不具合CSVを更新する。
2. `defect-trend-analysis` を再実行する。
3. 新しいPAT/RVをレビューして採用・却下する。
4. `regression-viewpoint-catalog.md` を更新する。
5. 次回テスト設計レビューで使う。

将来連携候補:

- Azure DevOps
- Jira
- GitHub Issues/PR
- テスト管理ツール

ただし、最初はCSV/Markdownで十分。

## 直近の推奨アクション

次にやるなら、Phase9を小さく始める。

1. 実不具合を50件だけ選ぶ。
2. 顧客名・個人名・本番IDをマスクする。
3. `real-defects.template.csv` に合わせる。
4. MVPを1回通す。
5. 出力を人間がレビューして、使える指摘と使えない指摘を分ける。

ここで価値が出ればPlugin化へ進む。価値が薄ければ、Skillを増やす前にtaxonomyと出力フォーマットを直す。

---

# 最終的に実装したいSkill一覧

最初から全部作らない。優先度は、実データで価値を確認しながら上から順に実装する。

## MVP Core Skills

### 1. `defect-trend-analysis`

概要:

- 過去不具合、テスト失敗、障害記録を分析し、繰り返し出る不具合パターンを抽出する。

主な入力:

- 不具合CSV
- Bug ticket一覧
- テスト失敗ログ
- 修正PR概要

主な出力:

- `defect-trend-report.md`
- `defect-pattern-catalog.yaml`

重要な設計:

- 事実、推測、仮説を分ける。
- 各パターンに根拠不具合IDを持たせる。
- root causeを断定しすぎない。

実装状況:

- Prototype済み。

### 2. `derive-regression-viewpoints`

概要:

- 不具合パターンを、再発防止のための回帰テスト観点に変換する。

主な入力:

- `defect-pattern-catalog.yaml`
- 既存テストケース
- 変更範囲
- リリース範囲

主な出力:

- `regression-viewpoint-catalog.md`
- `selected-regression-viewpoints.yaml`
- `missing-regression-tests.md`

重要な設計:

- 「全部回帰」ではなく、根拠付きで選定する。
- pattern ID、defect ID、viewpoint IDをつなぐ。
- coverageは根拠がなければ `unknown` にする。

実装状況:

- Prototype済み。

### 3. `test-design-review`

概要:

- テスト設計書やテストケース一覧を、不具合由来の回帰観点、テスト設計技法、業務リスクの観点でレビューする。

主な入力:

- テスト設計書
- テストケース一覧
- `defect-pattern-catalog.yaml`
- `regression-viewpoint-catalog.md`
- `selected-regression-viewpoints.yaml`

主な出力:

- `test-design-review-report.md`

重要な設計:

- 「テストを増やす」ではなく、足りない条件、データ、オラクル、組合せを具体的に指摘する。
- Critical/Highの指摘は必ずrequired changeを持つ。

実装状況:

- Prototype済み。

## Next Core Skills

### 4. `functional-test-design`

概要:

- 要件、仕様、チケットから機能テスト観点とテストケースを作る。
- 過去不具合由来の回帰観点も取り込む。

主な入力:

- 要件
- 仕様書
- acceptance criteria
- `regression-viewpoint-catalog.md`
- `defect-pattern-catalog.yaml`

主な出力:

- `functional-test-design.md`
- `test-conditions.md`
- `test-cases.csv`
- `decision-tables.md`
- `state-transition-tests.md`

使う技法:

- 同値分割
- 境界値分析
- デシジョンテーブル
- 状態遷移
- ユースケーステスト
- エラー推測
- 探索的テストチャーター

重要な設計:

- 技法ごとにユーザー入口を増やさない。
- ユーザーは `functional-test-design` だけ呼び、内部で必要な技法を選ぶ。

優先度:

- High。
- MVPが実データで使えると確認できた後に追加する。

### 5. `qa-intake`

概要:

- 入力資料を見て、どのQA Skillを使うべきか振り分ける入口。

主な入力:

- 不具合一覧
- 要件
- テスト設計書
- PR
- テスト結果
- チケット

主な出力:

- 推奨Skill
- 不足情報
- 想定リスク
- 次に作るべき成果物

振り分け例:

| 入力 | 推奨Skill |
|---|---|
| 不具合一覧 | `defect-trend-analysis` |
| defect pattern catalog | `derive-regression-viewpoints` |
| テスト設計書 | `test-design-review` |
| 仕様/要件 | `functional-test-design` |
| PR/code diff | `nfr-code-review` or future code review skill |

重要な設計:

- 入口は軽くする。
- 分析しすぎず、次に使うSkillと不足情報を返す。

優先度:

- Medium。
- Skill数が増えてから作る方が効果が出る。

## NFR / Review Skills

### 6. `nfr-code-review`

概要:

- コードや設計を非機能観点でレビューする。

主な入力:

- PR diff
- コード
- 設計書
- 変更概要

主な出力:

- `nfr-code-review-report.md`

対象観点:

- performance
- reliability
- security
- maintainability
- operability
- accessibility
- localization
- data integrity
- auditability

会計・業務アプリで重視する観点:

- 計算正確性
- データ整合性
- 監査証跡
- 再実行性
- 権限分離
- 帳票一致性
- Excel/API出力一致性

重要な設計:

- セキュリティは自前で抱え込みすぎない。
- Trail of Bits系や公式security-guidanceを専門参照として扱う。
- findingsはCritical/High/Medium/Lowで出す。

優先度:

- Medium。
- テスト設計系MVPが安定してから追加する。

### 7. `release-risk-judge`

概要:

- リリース前に、残不具合、テスト結果、未解決リスク、回帰観点の消化状況からリリースリスクを判定する。

主な入力:

- テスト実行結果
- 未解決不具合一覧
- 回帰観点一覧
- 既知リスク
- リリース範囲

主な出力:

- `release-risk-report.md`
- go / conditional go / no-go recommendation

重要な設計:

- 単純な合否判定ではなく、条件付きリリースや残リスクを明示する。
- 判断根拠を表にする。

優先度:

- Medium to Low。
- 実行結果データが取れるようになってから。

## Knowledge / Maintenance Skills

### 8. `defect-pattern-maintenance`

概要:

- `defect-pattern-catalog.yaml` を継続更新するためのメンテナンスSkill。

主な入力:

- 既存 `defect-pattern-catalog.yaml`
- 新規不具合CSV
- 人間レビュー結果

主な出力:

- 更新版 `defect-pattern-catalog.yaml`
- 追加/統合/retire候補

重要な設計:

- 似たパターンを乱立させない。
- 低価値な単発パターンは `monitor` や `retired` にする。
- 人間承認前提で更新する。

優先度:

- Medium。
- 実データ運用が始まってから必要になる。

### 9. `domain-glossary-builder`

概要:

- 不具合、仕様、テスト設計書からドメイン用語を抽出し、QAで使う用語集を作る。

主な入力:

- 仕様書
- 不具合一覧
- テスト設計書

主な出力:

- `domain-glossary.md`
- 用語、意味、関連機能、テスト時の注意点

重要な設計:

- 会計・業務ドメインの曖昧語を減らす。
- 同義語、略語、画面名、帳票名を整理する。

優先度:

- Low to Medium。
- 仕様理解のブレが大きい場合に有効。

### 10. `review-checklist-evolver`

概要:

- 過去レビュー指摘と不具合パターンから、自分用のテスト設計レビュー観点を更新する。

主な入力:

- `test-design-review-report.md`
- `defect-pattern-catalog.yaml`
- 人間レビューコメント

主な出力:

- `test-design-review-checklist.md` 更新案
- 追加すべきレビュー観点
- 削除/統合すべき観点

重要な設計:

- 自分のレビュー観点をナレッジ化する。
- 一般論ではなく、実際によく見落とす条件を蓄積する。

優先度:

- Medium。
- MVP運用後に価値が出る。

## Optional / Later Skills

### 11. `test-execution-summary`

概要:

- テスト実行結果を整理し、失敗傾向、未実行、ブロック、リスクをまとめる。

主な出力:

- `test-execution-summary.md`
- failure cluster
- blocked tests
- release risk notes

優先度:

- Low。
- テスト実行結果を安定して取得できるようになってから。

### 12. `automation-candidate-selector`

概要:

- 手動テストケースや回帰観点から、自動化候補を選ぶ。

判断軸:

- 実行頻度
- 安定性
- 重要度
- データ準備のしやすさ
- 自動化コスト
- flakiness risk

主な出力:

- `automation-candidates.md`
- 優先順位
- 自動化しない理由

優先度:

- Low to Medium。
- 回帰観点カタログが育ってから。

### 13. `traceability-check`

概要:

- 要件、テストケース、不具合、回帰観点のつながりを確認する。

主な出力:

- `traceability-matrix.md`
- orphan requirements
- orphan tests
- defect without regression viewpoint

優先度:

- Medium。
- 要件IDやテストケースIDが整備されている場合に有効。

## 実装優先順位

現時点の優先順位は次。

| Priority | Skill | 理由 |
|---:|---|---|
| 1 | `defect-trend-analysis` | 実装済みMVP。実データで最初に価値検証する |
| 2 | `derive-regression-viewpoints` | 実装済みMVP。不具合分析をテスト観点へ変換する中核 |
| 3 | `test-design-review` | 実装済みMVP。実際のレビュー価値を確認する |
| 4 | `functional-test-design` | 通常のテスト設計に広げるために必要 |
| 5 | `qa-intake` | Skill数が増えたときの入口 |
| 6 | `defect-pattern-maintenance` | 実データ運用でcatalogが育った後に必要 |
| 7 | `nfr-code-review` | 非機能・コードレビューへの拡張 |
| 8 | `traceability-check` | 要件/テストIDが整備されてから有効 |
| 9 | `release-risk-judge` | テスト実行結果と未解決不具合が揃ってから |
| 10 | `automation-candidate-selector` | 回帰観点カタログが育ってから |

## 最終形のイメージ

```text
qa-personal-skillset/
  skills/
    qa-intake/
    defect-trend-analysis/
    derive-regression-viewpoints/
    test-design-review/
    functional-test-design/
    nfr-code-review/
    defect-pattern-maintenance/
    traceability-check/
    release-risk-judge/
    automation-candidate-selector/

  references/
    defect-taxonomy.md
    evidence-classification.md
    regression-selection-criteria.md
    test-design-techniques.md
    test-design-review-checklist.md
    accounting-domain-quality-characteristics.md
    domain-glossary.md

  templates/
    defect-trend-report.md
    defect-pattern-catalog.yaml
    regression-viewpoint-catalog.md
    test-design-review-report.md
    functional-test-design.md
    traceability-matrix.md
    release-risk-report.md
```
