進め方としては、**いきなり全部インストールして使うより、まず「解体調査」→「部品化」→「自分用Skill設計」→「小さくPoC」** の順がいいと思う。

理由は単純で、Claude CodeのPluginはSkillだけでなく、agents、hooks、MCP servers、LSP servers、monitorsなども含められる構造になっているから。便利な反面、外部プラグインをそのまま社内PCや業務リポジトリに入れるのは、少しだけ無邪気すぎる。公式のプラグインディレクトリも、インストール前に信頼性を確認するよう明示している。([Claude Code][1])

---

# まず方針

きみが作りたいものは、たぶん単なる「QA便利コマンド集」ではなく、こういうものに近い。

```text
自分用 QA Skillset
=
テスト戦略
+ テスト設計
+ 不具合分析
+ 非機能レビュー
+ 自動化支援
+ ナレッジ蓄積
+ 自分のレビュー観点
```

なので、既存リポジトリは「採用候補」ではなく、**設計パターンの採掘対象**として見るのがよさそう。

---

# 対象リポジトリの役割分担

まず、リサーチ対象をこう分類する。

| リポジトリ                                      | 見るべき観点                                 | 自分用Skillsetへの使い道  |
| ------------------------------------------ | -------------------------------------- | ----------------- |
| `bmad-method-test-architecture-enterprise` | QA戦略、リスクベーステスト、NFR監査、Traceability      | 中核ワークフローの参考       |
| `sethdford/claude-skills`                  | テスト設計技法のSkill分割                        | 個別Skillの粒度設計      |
| `fugazi/test-automation-skills-agents`     | QA agents / automation skills          | 自動化支援・実装寄り        |
| `agile-v-skills`                           | 要件、検証、独立レビュー、トレーサビリティ                  | V字・検証ゲートの設計       |
| `jmagly/aiwg`                              | 多数のagents/skills/commands/rulesを配布する構造 | ナレッジ配布・マルチツール対応   |
| `claude-code/plugins` / 公式系                | Plugin構成、公式code-review                 | 構成・安全性・レビュー設計の基準  |
| `Iron-Ham/claude-deep-review`              | 性能、セキュリティ、アクセシビリティ等の専門レビュー             | 非機能コードレビューの部品     |
| `davila7/claude-code-templates`            | `qa-test-planner`                      | 軽量なQA計画Skillの参考   |
| `trailofbits/skills`                       | セキュリティ分析Skill                          | セキュリティ観点の専門知識     |
| `proffesor-for-testing/agentic-qe`         | QE agents、学習、Defect Intelligence       | 不具合分析・テスト自動化・学習機構 |

表記だけ補足すると、`jmagry/aiwg` ではなく **`jmagly/aiwg`**、`professor-for-testing/agentic-qe` ではなく **`proffesor-for-testing/agentic-qe`** が正しそう。後者は綴りがちょっと罠っぽい。罠というより、GitHubの森に生えた小さな落とし穴。

---

# 各リポジトリから「何を盗むか」

## 1. BMAD TEAから盗むもの

BMAD TEAは、Test Engineering Architectとして、リスクベースのテスト戦略、テスト自動化ガイダンス、リリースゲート判断を扱うモジュール。9つのワークフローを持ち、test design、framework setup、CI guidance、ATDD、automation、test review、NFR Evidence Audit、traceabilityなどを扱う。([GitHub][2])

ここから盗むべきなのは、**QA業務をワークフロー単位に分ける発想**。

たとえば自分用にはこう置き換える。

```text
/test-strategy
/test-design
/test-review
/nfr-assess
/traceability-check
/release-risk-judge
```

特に `nfr-assess` と `traceability` は、きみの仕事にかなり合うと思う。

---

## 2. sethdford/claude-skillsから盗むもの

この系統は、テスト設計技法を細かくSkill化する参考になる。

きみが作るなら、たとえばこう。

```text
/boundary-value-analysis
/equivalence-partitioning
/decision-table-design
/state-transition-design
/use-case-test-design
/error-guessing
/pairwise-design
```

ただし、全部をスラッシュコマンド化すると散らかる。
実際には、ユーザーが直接呼ぶのは `/functional-test-design` だけにして、内部参照として各技法ガイドを置く方がいい。

---

## 3. Fugaziから盗むもの

`fugazi/test-automation-skills-agents` は、Claude Code Plugin Marketplace経由でインストールでき、13個のQA agentsと10個のreusable skillsを提供する構成になっている。([GitHub][3])

ここから盗むべきなのは、**SkillだけでなくAgentも分ける構成**。

たとえば自分用にはこう。

```text
agents/
  test-strategist.md
  test-designer.md
  automation-architect.md
  regression-analyst.md
  defect-analyst.md
```

Skillは「手順」、Agentは「役割」として分ける。

---

## 4. Agile-Vから盗むもの

Agile-Vは、Claude Code、Cursor、VS Code、GitHub CopilotなどのAI coding toolsを対象に、要件、検証、テスト設計、Red Team的な検証を扱うSkill群として公開されている。([GitHub][4])

ここから盗むべきなのは、**実装者と検証者を分離する発想**。

自分用Skillsetでは、同じClaudeに全部やらせるとしても、役割を分けた方がいい。

```text
1. requirement-analyst が仕様を読む
2. risk-analyst がリスクを出す
3. test-designer がテスト観点を作る
4. independent-reviewer が抜け漏れをレビューする
```

これは人間のQAレビューにも近い。

---

## 5. AIWGから盗むもの

AIWGは、Claude Code、Copilot、Cursor、Warpなど複数プラットフォーム向けに、agents、skills、commands、rules、templatesなどを配布する構成を持つ。`aiwg use` によって各AIツールが読む場所へファイルをコピーするという説明になっている。([GitHub][5])

また、AIWG本体は200以上のagents、67以上のCLI commands、400以上のdeployable artifactsを含む大規模フレームワークとして説明されている。([GitHub][6])

ここから盗むべきなのは、**成果物・ルール・テンプレートを一箇所で管理し、必要な形式に展開する発想**。

きみが今後、Claude CodeだけでなくGitHub CopilotやCursorでも同じQA知識を使いたいなら、これはかなり重要。

---

## 6. 公式Claude Code Pluginsから盗むもの

Claude Code公式ドキュメントでは、SkillはClaudeの能力を拡張する仕組みで、custom commandsやbundled skillsを含められると説明されている。([Claude Code][7])

また、Pluginはskills、agents、hooks、MCP serversなどをまとめて配布できる単位。([Claude Code][1])

ここから盗むべきなのは、**最小構成の正しい作法**。

最初から巨大Pluginにせず、まずはこうでいい。

```text
qa-skillset/
  .claude-plugin/
    plugin.json
  skills/
    test-strategy/
      SKILL.md
    functional-test-design/
      SKILL.md
    defect-trend-analysis/
      SKILL.md
    nfr-code-review/
      SKILL.md
  references/
    test-design-techniques.md
    quality-characteristics.md
    defect-taxonomy.md
    domain-glossary.md
```

---

## 7. Iron-Ham Deep Reviewから盗むもの

`Iron-Ham/claude-deep-review` は、architecture analysis、error handling audit、type design、test coverage、accessibility、localization、concurrency、performance、security auditingなどをまとめた包括的コードレビューSkillとして説明されている。([GitHub][8])

ここから盗むべきなのは、**非機能レビュー観点の分解方法**。

自分用には、巨大な `/deep-review` を真似るより、こう分けた方がいい。

```text
/nfr-code-review
  - performance
  - security
  - accessibility
  - reliability
  - maintainability
  - operability
  - localization
  - observability
```

きみの対象が業務アプリなら、特に見るべきはこのあたり。

```text
performance
reliability
security
maintainability
operability
data-integrity
auditability
```

`auditability` はISO 25010そのものではないけど、会計ソフトではかなり大事。

---

## 8. davila7 / qa-test-plannerから盗むもの

`qa-test-planner` は、QAエンジニア向けにテスト計画、手動テストケース、回帰テストスイート、Figmaとの設計検証、バグ記録を扱うSkillとして説明されている。([GitHub][9])

ここから盗むべきなのは、**軽量な入口Skill**。

たとえば、最初にユーザーが呼ぶSkillはこれだけでいい。

```text
/qa-plan
```

その中で、必要に応じて内部的にこう分岐する。

```text
仕様が曖昧 → /requirement-analysis
リスクが必要 → /risk-analysis
テスト設計が必要 → /functional-test-design
回帰観点が必要 → /regression-viewpoint
非機能が必要 → /nfr-assess
```

---

## 9. Trail of Bitsから盗むもの

Trail of BitsのSkillsは、AI支援のセキュリティ分析、テスト、開発ワークフローを強化するClaude Code plugin marketplaceとして公開されている。([GitHub][10])

また、curated版では、品質と安全性を確認したClaude Code pluginsとして説明されている。([GitHub][11])

ここから盗むべきなのは、**セキュリティ観点だけは専門Skillに寄せる発想**。

QA Skillsetにセキュリティを全部抱え込むより、

```text
/security-review
/security-test-viewpoints
```

だけを自分用に作り、詳細な専門レビューはTrail of Bits系を参照する方がよさそう。

---

## 10. Agentic QEから盗むもの

`proffesor-for-testing/agentic-qe` は、Claude Codeと相性のよい、QA/QE向けのspecialized agents and skillsを持つオープンソースQA/QEプラットフォームとして説明されている。([GitHub][12])

FUNDING.md上では、19のSpecialized QE Agents、41のQE Skills、AI-powered learning、multi-model router、real-time visualizationなどが挙げられている。([GitHub][13])

ここから盗むべきなのは、**Defect IntelligenceとLearningの考え方**。

きみの用途なら、かなり重要。

```text
/defect-trend-analysis
/similar-defect-search
/root-cause-analysis
/regression-risk-analysis
/derive-test-viewpoints-from-defects
```

これは、自分用Skillsetの中でもかなり価値が出やすい部分だと思う。

---

# リサーチの進め方

## Phase 0：対象を固定する

まず、対象リポジトリを `research-targets.md` にまとめる。

```markdown
# Research Targets

## Core QA / Test Architecture
- bmad-code-org/bmad-method-test-architecture-enterprise
- proffesor-for-testing/agentic-qe

## Skill Collections
- sethdford/claude-skills
- davila7/claude-code-templates
- jmagly/aiwg

## Automation
- fugazi/test-automation-skills-agents

## Verification / Process
- Agile-V/agile_v_skills

## Code Review / NFR / Security
- anthropics/claude-plugins-official
- Iron-Ham/claude-deep-review
- trailofbits/skills
```

この時点ではインストールしない。まず読むだけ。

---

## Phase 1：ローカルにcloneして棚卸しする

作業フォルダを分ける。

```text
qa-skillset-research/
  sources/
  notes/
  extracted-patterns/
  prototype/
```

cloneする。

```bash
mkdir qa-skillset-research
cd qa-skillset-research
mkdir sources notes extracted-patterns prototype
cd sources

git clone https://github.com/bmad-code-org/bmad-method-test-architecture-enterprise.git
git clone https://github.com/sethdford/claude-skills.git
git clone https://github.com/fugazi/test-automation-skills-agents.git
git clone https://github.com/Agile-V/agile_v_skills.git
git clone https://github.com/jmagly/aiwg.git
git clone https://github.com/anthropics/claude-plugins-official.git
git clone https://github.com/Iron-Ham/claude-deep-review.git
git clone https://github.com/davila7/claude-code-templates.git
git clone https://github.com/trailofbits/skills.git
git clone https://github.com/proffesor-for-testing/agentic-qe.git
```

---

## Phase 2：各リポジトリを同じ観点で読む

各リポジトリごとに、次のテンプレートでメモを作る。

```markdown
# Repository Review: <repo-name>

## 1. 概要
- 何をするものか
- Claude Code Plugin / Skill / Command / Agent / MCP のどれか

## 2. 含まれる構成要素
- skills:
- commands:
- agents:
- hooks:
- MCP:
- scripts:

## 3. QA業務への関連
- テスト戦略:
- テスト設計:
- 不具合分析:
- 非機能:
- 自動化:
- レビュー:
- ナレッジ化:

## 4. 盗みたい設計
- Skillの粒度:
- 入力の取り方:
- 出力フォーマット:
- ワークフロー:
- 評価・ゲート:
- ナレッジ参照:

## 5. そのまま使わない方がよい点
- 過剰:
- 業務に合わない:
- セキュリティ懸念:
- 依存ツール:
- 保守性:

## 6. 自分用Skillsetへの取り込み方
- そのまま参考:
- 改変して採用:
- アイデアのみ採用:
- 不採用:
```

このテンプレートで見ると、「なんか良さそう」で終わらなくなる。

---

# 評価軸

各リポジトリやSkillを、5段階で採点する。

| 評価軸   | 見ること                        |
| ----- | --------------------------- |
| 業務適合度 | きみのQA業務に近いか                 |
| 再利用性  | 社内ナレッジに置き換えやすいか             |
| 粒度    | Skillとして大きすぎないか、小さすぎないか     |
| 入力設計  | 仕様書、チケット、PR、テスト結果を扱えるか      |
| 出力品質  | テスト設計者が使える成果物になるか           |
| 検証可能性 | 根拠、トレーサビリティ、確信度を出せるか        |
| 安全性   | hooks/scripts/MCPなどのリスクがないか |
| 保守性   | 自分で更新し続けられるか                |

点数表はこう。

```markdown
| Repo | 業務適合 | 再利用 | 粒度 | 入力設計 | 出力品質 | 検証可能性 | 安全性 | 保守性 | コメント |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---|
| BMAD TEA | 5 | 4 | 4 | 4 | 4 | 5 | 3 | 4 | 中核候補 |
```

ここで大事なのは、**星の数で勝者を決めないこと**。
BMAD TEAは中核、Trail of Bitsはセキュリティ、Iron-Hamは非機能レビュー、Agentic QEは不具合分析、というように役割が違う。

---

# 自分用Skillsetの最小構成

最初のバージョンは、これくらいでいいと思う。

```text
qa-personal-skillset/
  .claude-plugin/
    plugin.json

  skills/
    qa-intake/
      SKILL.md
    test-strategy/
      SKILL.md
    functional-test-design/
      SKILL.md
    defect-trend-analysis/
      SKILL.md
    derive-regression-viewpoints/
      SKILL.md
    nfr-code-review/
      SKILL.md
    test-design-review/
      SKILL.md

  agents/
    requirement-analyst.md
    risk-analyst.md
    test-designer.md
    defect-analyst.md
    nfr-reviewer.md
    independent-reviewer.md

  references/
    qa-principles.md
    test-design-techniques.md
    iso25010-quality-characteristics.md
    defect-taxonomy.md
    regression-viewpoint-catalog.md
    domain-glossary.md
    review-checklist.md

  templates/
    test-strategy-report.md
    functional-test-design.md
    defect-trend-report.md
    regression-viewpoint-catalog.md
    nfr-review-report.md
```

呼び出し口は多すぎない方がいい。

最初はこの7つくらい。

```text
/qa-intake
/test-strategy
/functional-test-design
/defect-trend-analysis
/derive-regression-viewpoints
/nfr-code-review
/test-design-review
```

---

# それぞれのSkillの役割

## `/qa-intake`

入力資料を見て、何をすべきか振り分ける入口。

```text
入力:
- 仕様書
- 要件
- チケット
- PR
- テスト結果
- 不具合一覧

出力:
- どのSkillを使うべきか
- 不足している情報
- 想定リスク
- 次の作業単位
```

## `/test-strategy`

BMAD TEA寄り。

```text
出力:
- 対象範囲
- 品質リスク
- テスト種別
- 重点観点
- 除外範囲
- 非機能要否
- 品質ゲート
```

## `/functional-test-design`

sethdford系のテスト設計技法を統合する。

```text
出力:
- 機能一覧
- 条件一覧
- 同値分割
- 境界値
- 状態遷移
- デシジョンテーブル
- ユースケース
- 異常系
- テスト観点
```

## `/defect-trend-analysis`

Agentic QE寄り。

```text
出力:
- 不具合分類
- 頻出パターン
- 類似クラスタ
- 発生条件
- 根本原因仮説
- 流出原因
- 影響機能
- 再発防止候補
```

## `/derive-regression-viewpoints`

不具合分析をテスト観点へ変換する。

```text
入力:
- 不具合一覧
- 修正PR
- 過去テストケース
- 類似機能一覧

出力:
- 回帰テスト観点
- 横展開すべき機能
- 優先度
- 根拠チケット
- 推測と事実の分離
```

## `/nfr-code-review`

Iron-Ham、Trail of Bits、公式code-reviewのいいとこ取り。

```text
観点:
- performance
- reliability
- security
- maintainability
- operability
- accessibility
- localization
- data-integrity
- auditability
```

## `/test-design-review`

きみ自身のレビュー観点を入れる場所。

```text
出力:
- 抜け漏れ
- 過剰なテスト
- リスクに対する不足
- 仕様理解の誤り
- 境界値不足
- 状態・データ条件不足
- 過去不具合観点の不足
- S/A/B/C/D評価
```

---

# 最初のPoCでやること

最初から全部作らない方がいい。
おすすめは、**不具合分析 → 回帰観点化** から始めること。

理由は、きみの職場で価値が説明しやすいから。

```text
過去不具合チケットを読む
      ↓
頻出パターンを分類する
      ↓
見落とし条件を抽出する
      ↓
再発防止テスト観点に変換する
      ↓
次回のテスト設計レビューで使う
```

これは「AIでなんか便利にする」ではなく、**過去の失敗を組織のテスト設計能力に変換する**と言える。提案として強い。

---

# PoCの入力データ

まずは過去不具合50〜100件でいい。

必要な項目はこの程度。

```csv
id,title,description,feature,detected_phase,severity,root_cause,fix_summary,related_pr,test_gap
BUG-001,期末日の集計誤り,...,連結処理,ST,High,境界条件漏れ,...,PR-123,期末境界の観点不足
```

最初からAzure DevOps MCPに繋がなくてもいい。
CSVやMarkdownで十分。

その後、Azure DevOps MCPと接続すればいい。

---

# 成果物イメージ

PoCで出す成果物は、これだけに絞る。

```text
1. defect-trend-report.md
2. defect-pattern-catalog.yaml
3. regression-viewpoint-catalog.md
4. test-design-review-checklist.md
```

特に重要なのは `regression-viewpoint-catalog.md`。

例。

```markdown
# 回帰テスト観点カタログ

## 期間境界での集計漏れ

### 根拠
- BUG-1234
- BUG-1892
- BUG-2041

### 発生しやすい条件
- 期首・期末
- 比較年度切替
- 遡及修正
- 0件データ
- 複数会社・複数通貨

### 横展開対象
- 連結処理
- 帳票出力
- Excelアドイン
- 仕訳生成

### 推奨テスト観点
- 期首・期末を跨ぐデータ
- 前期・当期・翌期の比較
- データなし、1件、複数件
- 修正仕訳あり・なし
- 再実行時の結果一致
```

これができると、次に `/functional-test-design` や `/test-design-review` に食わせられる。

---

# リサーチ時に見落とさない方がいいこと

## 1. Promptの中身だけでなく、出力フォーマットを見る

よいSkillは、指示文がうまいだけでなく、出力が再利用しやすい。

見るべきはここ。

```text
- Markdown表か
- YAMLか
- JSONか
- チェックリストか
- レポート形式か
- 後続Skillに渡しやすいか
```

きみの用途では、最終的にナレッジ化したいので、**YAML + Markdown** がよさそう。

---

## 2. 「推測」と「根拠」を分けているか見る

不具合分析系では特に重要。

```yaml
evidence_level: confirmed | likely | hypothesis
sources:
  - BUG-1234
  - PR-567
```

これがないSkillは、もっともらしい反省文を量産しがち。
QAにとって一番困るのは、間違っているけど読みやすい分析だからね。

---

## 3. Skillを細かくしすぎない

`boundary-value-analysis` や `state-transition-testing` を全部コマンド化すると、使う側が迷う。

おすすめはこう。

```text
ユーザーが呼ぶSkill:
- /functional-test-design

内部参照:
- boundary-value-analysis.md
- equivalence-partitioning.md
- decision-table.md
- state-transition.md
```

人間が操作する入口は少なく、AIが読む知識は多く。

---

## 4. 自分のレビュー観点を最初から入れる

既存Skillを調べると、どうしても一般論になる。

でも、きみの価値は一般論ではなく、

```text
連結会計ソフトで過去に何を見落としたか
自分がレビューで何をよく指摘するか
自社のテスト設計で何が弱いか
```

にある。

だから、最初から `review-checklist.md` を作るべき。

---

# 具体的な作業順

## Step 1：リポジトリ棚卸し

成果物。

```text
notes/repo-review-*.md
notes/repo-score-matrix.md
```

この段階では、良し悪しを決めるより「どの部品が使えるか」を見る。

---

## Step 2：Skill候補リストを作る

成果物。

```text
extracted-patterns/skill-candidates.md
```

形式。

```markdown
# Skill Candidates

## defect-trend-analysis
参考:
- agentic-qe
- BMAD RCA
- Agile-V

採用したい要素:
- defect clustering
- root cause
- regression impact
- evidence level

採用しない要素:
- 過剰なmulti-agent orchestration
- 外部依存が重い機能
```

---

## Step 3：自分用SkillsetのMVPを決める

最初はこれだけでいい。

```text
/defect-trend-analysis
/derive-regression-viewpoints
/test-design-review
```

この3つはつながりがある。

```text
不具合分析
  ↓
再発防止観点化
  ↓
テスト設計レビューに適用
```

---

## Step 4：参照ナレッジを作る

最低限、これ。

```text
references/
  defect-taxonomy.md
  regression-viewpoint-catalog.md
  test-design-review-checklist.md
  quality-characteristics.md
```

`quality-characteristics.md` にはISO 25010を入れてもいいけど、会計システム向けにこういう観点も足した方がいい。

```text
- 計算正確性
- データ整合性
- 監査証跡
- 再実行性
- 期間境界
- 権限分離
- 帳票一致性
- Excel出力再現性
```

---

## Step 5：実データで評価する

過去不具合50件で試す。

評価観点はこれ。

```text
- 人間が見ても妥当な分類か
- 類似不具合クラスタが有用か
- 新しいテスト観点が出たか
- 根拠チケットが明記されているか
- 推測が混ざりすぎていないか
- 次回レビューで使える形か
```

AIの出力を見て「まあ賢そう」ではなく、**次のテスト設計レビューで実際に使えるか**で見る。

---

# 最終的なロードマップ

```text
Week 1:
  リポジトリ調査
  Skill構成の棚卸し
  採用パターン整理

Week 2:
  defect-trend-analysis のMVP作成
  derive-regression-viewpoints のMVP作成
  過去不具合50件で試行

Week 3:
  test-design-review に過去不具合観点を統合
  実際のテスト設計書をレビュー
  人間レビューとの差分を見る

Week 4:
  test-strategy / functional-test-design を追加
  BMAD TEA風の上流ワークフローを統合

Week 5以降:
  Azure DevOps MCP接続
  PRレビュー・非機能レビュー連携
  社内向けPlugin化
```

---

# 私なら最初に作るリポジトリ

```text
qa-agent-skills/
  README.md

  research/
    targets.md
    repo-score-matrix.md
    findings.md

  plugin/
    .claude-plugin/
      plugin.json

    skills/
      defect-trend-analysis/
        SKILL.md
      derive-regression-viewpoints/
        SKILL.md
      test-design-review/
        SKILL.md

    references/
      defect-taxonomy.md
      regression-viewpoint-catalog.md
      test-design-review-checklist.md
      quality-characteristics.md
      domain-glossary.md

    templates/
      defect-trend-report.md
      regression-viewpoint-catalog.md
      test-design-review-report.md

  samples/
    defects.sample.csv
    test-design.sample.md
```

最初から巨大な「QA全部入り」を作らない。
不具合分析からテスト観点へ還元する1本の流れを作る。

これが動けば、あとからテスト戦略、非機能レビュー、テスト自動化を足せる。逆にここが動かないと、上にどれだけ立派なPlugin構造を載せても、ちょっと豪華な空き箱になる。

[1]: https://code.claude.com/docs/en/plugins-reference?utm_source=chatgpt.com "Plugins reference - Claude Code Docs"
[2]: https://github.com/bmad-code-org/bmad-method-test-architecture-enterprise?utm_source=chatgpt.com "Test Architect Full BMad Method Enhancement · GitHub"
[3]: https://github.com/fugazi/test-automation-skills-agents?utm_source=chatgpt.com "fugazi/test-automation-skills-agents: A practical library of ..."
[4]: https://github.com/Agile-V/agile_v_skills?utm_source=chatgpt.com "Agile-V/agile_v_skills: 🔬 Verifiable AI-Augmented ..."
[5]: https://github.com/jmagly/aiwg/blob/main/CLAUDE.md?utm_source=chatgpt.com "CLAUDE.md - jmagly/aiwg"
[6]: https://github.com/jmagly/aiwg?utm_source=chatgpt.com "jmagly/aiwg: Cognitive architecture for AI- ..."
[7]: https://code.claude.com/docs/en/skills?utm_source=chatgpt.com "Extend Claude with skills - Claude Code Docs"
[8]: https://github.com/iron-ham/claude-deep-review?utm_source=chatgpt.com "Iron-Ham/claude-deep-review"
[9]: https://github.com/davila7/claude-code-templates/blob/main/cli-tool/components/skills/ai-research/qa-test-planner/SKILL.md?utm_source=chatgpt.com "QA Test Planner - claude-code-templates"
[10]: https://github.com/trailofbits/skills?utm_source=chatgpt.com "trailofbits/skills: Trail of Bits Claude Code skills for security ..."
[11]: https://github.com/trailofbits/skills-curated?utm_source=chatgpt.com "trailofbits/skills-curated: Curated, community-vetted Claude ..."
[12]: https://github.com/proffesor-for-testing/agentic-qe?utm_source=chatgpt.com "proffesor-for-testing/agentic-qe: Agentic QE Fleet ..."
[13]: https://github.com/proffesor-for-testing/agentic-qe/blob/main/FUNDING.md?utm_source=chatgpt.com "FUNDING.md - proffesor-for-testing/agentic-qe"
