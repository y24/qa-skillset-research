# Repository Score Matrix

Phase2 score for the cloned repositories. Scores are 1-5 and reflect usefulness for a personal QA Skillset, not repository quality in general.

## Scoring Criteria

| Axis | Meaning |
|---|---|
| 業務適合 | QA実務、特に業務アプリ・会計系のテスト設計やレビューに近いか |
| 再利用 | 自分用Skillsetへ部品として移植しやすいか |
| 粒度 | Skill/Agent/Workflowの単位が大きすぎず小さすぎないか |
| 入力設計 | 仕様書、チケット、PR、テスト結果、不具合一覧を扱いやすいか |
| 出力品質 | テスト設計者・レビュー担当者がそのまま使える成果物になるか |
| 検証可能性 | 根拠、トレーサビリティ、評価基準、ゲートが明示されているか |
| 安全性 | hooks/scripts/MCP/外部依存をそのまま持ち込むリスクが低いか |
| 保守性 | 自分で小さく維持・更新し続けられるか |

## Matrix

| Repo | 主用途 | 業務適合 | 再利用 | 粒度 | 入力設計 | 出力品質 | 検証可能性 | 安全性 | 保守性 | 合計 | 判定 | コメント |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|---|
| `bmad-method-test-architecture-enterprise` | QA戦略・リスク・NFR・Traceability | 5 | 4 | 4 | 4 | 5 | 5 | 3 | 3 | 33 | 中核参考 | 9個のTEA workflowが最も体系的。step-file構成、create/edit/validate、checklist、templateの作りは強く参考にする。 |
| `proffesor-for-testing/agentic-qe` | QE agents、Defect Intelligence、学習 | 5 | 3 | 3 | 4 | 4 | 4 | 2 | 2 | 27 | 部品採用 | defect intelligence、root cause、regression analyzer、evidence分類が有用。ただし規模と外部CLI前提が重い。 |
| `sethdford/claude-skills` | QA技法別Skill集 | 4 | 5 | 4 | 3 | 4 | 3 | 5 | 4 | 32 | 高優先 | functional testing、quality metrics、test strategyの粒度設計が良い。個別Skillをそのまま入口にせず内部参照化する。 |
| `fugazi/test-automation-skills-agents` | QA automation skills/agents | 4 | 4 | 4 | 4 | 4 | 3 | 4 | 4 | 31 | 高優先 | ISTQB toolkit、qa-test-planner、Playwright regressionが実務成果物に近い。テンプレート流用価値が高い。 |
| `Agile-V/agile_v_skills` | 検証ゲート・独立検証・traceability | 4 | 3 | 3 | 4 | 4 | 5 | 3 | 3 | 29 | 構造採用 | REQ->ART->TC、Human Gate、Red Team Verifier、regression selectionの発想を採用。全体思想は重め。 |
| `jmagly/aiwg` | マルチプラットフォーム配布・artifact管理 | 3 | 3 | 2 | 3 | 3 | 4 | 2 | 2 | 22 | アイデア採用 | one source of truthを複数AIツールに展開する発想は有用。規模が大きく、MVPに直接入れると過剰。 |
| `anthropics/claude-plugins-official` | 公式Plugin構成・code/security review | 3 | 4 | 4 | 4 | 4 | 4 | 3 | 4 | 30 | 基準採用 | plugin構造、code-reviewの多視点agentとconfidence filter、security-guidanceの層構造が設計基準になる。 |
| `Iron-Ham/claude-deep-review` | NFR/専門コードレビュー | 4 | 3 | 3 | 4 | 4 | 4 | 3 | 3 | 28 | 部品採用 | performance/security/a11y/l10n/concurrency/test等の分解が良い。49 agent構成はMVPでは過剰。 |
| `davila7/claude-code-templates` | 軽量QA planner・テンプレート集 | 3 | 4 | 4 | 3 | 4 | 3 | 3 | 3 | 27 | 入口参考 | qa-test-plannerは軽量な入口Skillとして参考。リポジトリ全体はQA以外が多くノイズも多い。 |
| `trailofbits/skills` | Security testing・static analysis | 3 | 4 | 4 | 3 | 4 | 5 | 3 | 4 | 30 | 専門参照 | property-based testing、static analysis、semgrep、variant analysisが強い。QA Skillsetではsecurity専門参照に留める。 |

## Role-Based Ranking

| Role in Personal QA Skillset | Primary References | Notes |
|---|---|---|
| 中核ワークフロー | `bmad-method-test-architecture-enterprise`, `Agile-V/agile_v_skills` | test strategy、risk、traceability、quality gateの骨格。 |
| テスト設計技法 | `sethdford/claude-skills`, `fugazi/test-automation-skills-agents` | EP/BVA/decision table/state transitionを内部参照化。 |
| 不具合分析・回帰観点化 | `proffesor-for-testing/agentic-qe`, `sethdford/claude-skills` | defect intelligence、RCA、escaped defect analysis、regression analyzer。 |
| 自動化支援 | `fugazi/test-automation-skills-agents`, `trailofbits/skills` | Playwright regression、API testing、property-based testing。 |
| 非機能・セキュリティレビュー | `Iron-Ham/claude-deep-review`, `trailofbits/skills`, `claude-plugins-official` | NFR観点分解、security guidance、confidence/evidenceの扱い。 |
| 配布・運用構造 | `claude-plugins-official`, `jmagly/aiwg` | plugin最小構成と、将来のmulti-platform展開。 |

## Recommendation

MVPでは次の順で採用する。

1. `defect-trend-analysis`: Agentic QE + sethdford quality metricsを参考に、不具合分類・RCA・根拠分離を作る。
2. `derive-regression-viewpoints`: Agile-V regression selection + fugazi regression suiteを参考に、過去不具合から回帰観点へ変換する。
3. `test-design-review`: BMAD test-review + sethdford/fugazi test design techniquesを参考に、テスト設計レビューのチェックリストを作る。
4. `nfr-code-review`: Iron-Ham + Trail of Bits + official security-guidanceを参考に、後続フェーズで追加する。
