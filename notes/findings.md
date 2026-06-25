# Phase2 Findings

Phase2では、clone済みの各リポジトリを同じ観点で読み、個人用QA Skillsetへ取り込むべき設計パターンを整理した。結論として、最初のMVPは「不具合分析 -> 回帰観点化 -> テスト設計レビュー」に絞るのがよい。

## Executive Summary

最も中核に置くべきなのは `bmad-method-test-architecture-enterprise` のstep-file型workflowである。`SKILL.md` を入口にし、`steps-c/`, `steps-e/`, `steps-v/`, `checklist.md`, `templates/` に分ける構造は、QA成果物を作るSkillに向いている。

`proffesor-for-testing/agentic-qe` は、不具合予測、RCA、パターン学習、回帰分析の発想が強い。ただし、CLI・多agent・学習基盤まで含むため、そのまま導入するより、Defect Intelligenceの出力項目とEvidence分類だけを取り込むのが現実的。

`sethdford/claude-skills` と `fugazi/test-automation-skills-agents` は、実務QAの粒度とテンプレートが使いやすい。特に、ISTQB型のtest plan/test case/bug report/traceability matrix/regression suiteは、個人Skillsetのテンプレートとして再利用しやすい。

`Agile-V/agile_v_skills` は、要件、実装、テスト、証跡を分離する思想がよい。MVPでは全体フレームワークを持ち込まず、`REQ -> test -> defect -> regression rationale` の証跡設計だけ採用する。

`Iron-Ham/claude-deep-review`, `trailofbits/skills`, `claude-plugins-official` は、非機能・セキュリティ・コードレビューの専門部品として扱う。最初から統合すると範囲が広がりすぎるため、MVP後の `nfr-code-review` で使う。

## Repository Notes

### `bmad-method-test-architecture-enterprise`

見るべきもの:

- `src/workflows/testarch/` に9個のTEA workflowがある。
- 各workflowは `SKILL.md`, `customize.toml`, `workflow-plan.md`, `workflow.yaml`, `instructions.md`, `checklist.md`, `steps-c/`, `steps-e/`, `steps-v/`, `templates/` で構成される。
- create/edit/validateを分け、1 stepずつ読ませることでLLMの逸脱を抑える設計になっている。

盗みたい設計:

- `defect-trend-analysis` や `test-design-review` も、単一の長い指示ではなく step-file型にする。
- validate modeを必ず持たせ、成果物の根拠、抜け漏れ、曖昧さを検査する。
- checklistとtemplateをSkill本体から分離する。

そのまま使わない点:

- TEA全体は企業向けに重い。個人MVPではworkflow数を増やしすぎない。

### `proffesor-for-testing/agentic-qe`

見るべきもの:

- `assets/skills/qe-defect-intelligence/SKILL.md`
- `assets/skills/regression-testing/`
- `assets/agents/v3/qe-defect-predictor.md`
- `assets/agents/v3/qe-root-cause-analyzer.md`
- `assets/agents/v3/qe-regression-analyzer.md`
- `assets/agents/v3/helpers/quality-criteria/evidence-classification.md`

盗みたい設計:

- 不具合分析で `historicalDefects`, `patterns`, `recommendations`, `riskScore`, `riskLevel`, `factors` を分ける。
- RCAでは `five-whys`, `fishbone`, `fault-tree`, `change-impact` を選択肢として扱う。
- Evidenceを `Direct`, `Inferred`, `Claimed` に分ける。特に、推測を「要検証」と明示するルールはQA用途で重要。

そのまま使わない点:

- `aqe` CLI、multi-model router、real-time visualizationなどはMVPには不要。
- riskScoreのような数値は、根拠が薄いともっともらしいだけになる。最初はS/A/B/CやHigh/Medium/Low程度に抑える。

### `sethdford/claude-skills`

見るべきもの:

- `qa-engineer/README.md` は63 skills、26 commands、8 pluginsのQA Skill集。
- `functional-testing/skills/` にBVA、同値分割、デシジョンテーブル、状態遷移、ユースケース、エラー推測が分かれている。
- `quality-metrics/skills/` にdefect analysis、escaped defect analysis、root cause analysis、quality gate designがある。
- `test-strategy/skills/` にrisk-based test plan、regression strategy、coverage analysisがある。

盗みたい設計:

- テスト設計技法は独立ファイルとして持つ。
- ユーザー入口は `/functional-test-design` など少数にし、技法別Skillは内部参照にする。
- quality metrics系は `defect-trend-analysis` の参考にする。

そのまま使わない点:

- 細かいSkillをそのまま全部公開すると、使う側がどれを呼ぶべきか迷う。

### `fugazi/test-automation-skills-agents`

見るべきもの:

- `skills/qa-manual-istqb/SKILL.md`
- `skills/qa-test-planner/SKILL.md`
- `skills/playwright-regression-testing/SKILL.md`
- `agents/qa-orchestrator.agent.md`
- `agents/playwright-test-planner.agent.md`
- `agents/flaky-test-hunter.agent.md`

盗みたい設計:

- ISTQB alignedな「Test Planning -> Test Analysis -> Test Design -> Test Implementation -> Test Execution -> Test Completion」の流れ。
- `test-plan.md`, `test-cases.csv`, `traceability-matrix.csv`, `bug-report.md`, `regression-suite.md` のテンプレート構成。
- 回帰テストをrisk + frequency + criticality + defect historyで選ぶ発想。

そのまま使わない点:

- Playwright/Seleniumなど実装寄りの依存は、最初の不具合分析MVPでは不要。

### `Agile-V/agile_v_skills`

見るべきもの:

- `skills/graph-traceability-agent/`
- `skills/regression-selection-agent/`
- `skills/impact-analysis-agent/`
- `skills/diff-evidence-agent/`
- `red-team-verifier/`, `requirement-architect/`, `test-designer/`

盗みたい設計:

- Build AgentとVerifierを分ける独立検証の考え方。
- `REQ -> ART -> TC` のtraceabilityを、個人Skillsetでは `Requirement/Spec -> Test Viewpoint -> Test Case -> Defect -> Regression Viewpoint` に置き換える。
- `required_regression_tests.md`, `selected_tests.json`, `regression_coverage_rationale.md`, `missing_regression_tests.md` のように、出力を人間用と機械用に分ける。

そのまま使わない点:

- フレームワーク全体は形式が強い。個人作業では、人間ゲートと証跡だけを軽量に取り込む。

### `jmagly/aiwg`

見るべきもの:

- `aiwg use` による各AIツール向け展開。
- Agents、Skills、Commands、Rules、Behaviorsをplain text artifactとして管理する構造。
- `.aiwg/` を共有memory/artifact置き場にする発想。

盗みたい設計:

- one source of truthを将来的にClaude Code、Codex、Cursor、GitHub Copilot向けへ展開する発想。
- `references/`, `templates/`, `rules/` を分け、Skill本体を薄く保つ。

そのまま使わない点:

- 200+ agents、400+ artifacts規模はMVPには過剰。
- hooksやdaemonは業務PCでは安全性レビューなしに入れない。

### `anthropics/claude-plugins-official`

見るべきもの:

- `plugins/code-review/README.md`
- `plugins/security-guidance/README.md`
- `plugins/skill-creator/`
- `plugins/plugin-dev/`

盗みたい設計:

- code-reviewの複数agentによる独立レビューとconfidence threshold。
- security-guidanceの3層構造: pattern warning、LLM diff review、agentic commit review。
- project/user/localのポリシーファイルを読む構成。

そのまま使わない点:

- hookやcommit reviewは便利だが、送信データや実行タイミングの確認が必要。個人QA Skillsetでは最初は手動起動にする。

### `Iron-Ham/claude-deep-review`

見るべきもの:

- `skills/deep-review/SKILL.md`
- `skills/deep-review/agents/test-analyzer.md`
- `skills/deep-review/agents/performance-analyzer.md`
- `skills/deep-review/agents/security-reviewer.md`
- `skills/deep-review/agents/accessibility-scanner.md`
- `skills/deep-review/agents/localization-scanner.md`
- `skills/deep-review/agents/silent-failure-hunter.md`

盗みたい設計:

- NFRレビュー観点の分解。
- architecture、tests、security、performance、concurrency、a11y、l10nを個別agentに分け、最後にsynthesizerで統合する流れ。
- FindingをCritical/Important/Suggestionに分ける。

そのまま使わない点:

- 49 agentsをそのまま採用しない。会計・業務アプリ向けには `data-integrity`, `auditability`, `re-executability`, `authorization`, `report consistency` を足した小型版にする。

### `davila7/claude-code-templates`

見るべきもの:

- `cli-tool/components/skills/ai-research/qa-test-planner/`

盗みたい設計:

- 明示起動の軽量な `/qa-test-planner`。
- Analyze -> Generate -> Validateの3相構成。
- test plan、manual test case、regression suite、Figma validation、bug reportという入口のわかりやすさ。

そのまま使わない点:

- リポジトリ全体はQA以外のcomponentが多い。qa-test-plannerだけを参考にする。

### `trailofbits/skills`

見るべきもの:

- `plugins/property-based-testing/`
- `plugins/static-analysis/`
- `plugins/semgrep-rule-creator/`
- `plugins/variant-analysis/`
- `plugins/testing-handbook-skills/`
- `plugins/insecure-defaults/`
- `plugins/supply-chain-risk-auditor/`

盗みたい設計:

- セキュリティ観点は専門Skillとして分離する。
- Semgrepやproperty-based testingのように、検出ロジックをテスト可能な形に落とす。
- variant analysisは「類似不具合の横展開」に応用できる。

そのまま使わない点:

- セキュリティ監査系は専門性と前提ツールが強い。会計QA Skillsetでは参照先として扱い、自前Skillに抱え込みすぎない。

## Cross-Repository Patterns

### 1. 良いSkillは入口が薄く、参照が厚い

BMAD、fugazi、sethdfordに共通して、Skill本体に全部を書かず、references、templates、checklistsへ分離している。個人Skillsetでも次の形にする。

```text
skills/
  defect-trend-analysis/
    SKILL.md
    steps/
    checklist.md
templates/
references/
```

### 2. 出力はMarkdownだけでなくYAML/JSONも持つ

人間が読む報告書だけだと後続Skillに渡しにくい。Agile-VやAgentic QEのように、次の2系統を分ける。

```text
defect-trend-report.md      # 人間用
defect-pattern-catalog.yaml # 後続Skill用
```

### 3. 推測と根拠を分ける必要がある

Agentic QEのEvidence分類はそのまま採用価値が高い。QA成果物では以下を必須にする。

```yaml
evidence:
  type: direct | inferred | claimed
  source: BUG-1234
  quote_or_observation: ""
  reasoning: ""
  needs_verification: true
```

### 4. 回帰観点は「全部再実行」ではなく選択根拠が重要

fugaziとAgile-Vに共通して、回帰テストはrisk、change impact、criticality、defect historyで選ぶ。MVPの `derive-regression-viewpoints` では、観点ごとに根拠チケットと横展開先を持たせる。

### 5. 多agent構成は最初から入れない

Iron-Ham、Agentic QE、AIWGは多agent構成が強いが、MVPでは保守コストが大きい。まずはSkillとreferencesで動く形にして、必要になったら `defect-analyst`, `regression-analyst`, `independent-reviewer` を足す。

## Recommended MVP Design

```text
plugin/
  skills/
    defect-trend-analysis/
      SKILL.md
      steps/
        step-01-input-check.md
        step-02-classify-defects.md
        step-03-find-patterns.md
        step-04-root-cause-hypotheses.md
        step-05-write-report.md
      checklist.md
    derive-regression-viewpoints/
      SKILL.md
      steps/
      checklist.md
    test-design-review/
      SKILL.md
      steps/
      checklist.md
  references/
    defect-taxonomy.md
    evidence-classification.md
    test-design-techniques.md
    regression-selection-criteria.md
    accounting-domain-quality-characteristics.md
  templates/
    defect-trend-report.md
    defect-pattern-catalog.yaml
    regression-viewpoint-catalog.md
    test-design-review-report.md
```

## Suggested Outputs for Next Phase

Phase3では、次のファイルを作るのがよい。

1. `extracted-patterns/skill-candidates.md`
2. `extracted-patterns/output-templates.md`
3. `extracted-patterns/evidence-model.md`
4. `prototype/plugin/skills/defect-trend-analysis/SKILL.md`
5. `prototype/plugin/references/defect-taxonomy.md`

## Adoption Decision

| Area | Adopt | Modify | Avoid |
|---|---|---|---|
| Workflow structure | BMAD step-file architecture | 個人用にsteps数を5前後へ縮小 | 9 workflow全部を移植 |
| Test design techniques | sethdford/fugazi references | 技法別Skillは内部参照化 | 技法ごとにユーザー入口を増やす |
| Defect intelligence | Agentic QE concepts | scoreは粗く、evidence重視 | ML/CLI前提の実装を初期導入 |
| Traceability | Agile-V evidence chain | 業務QA向けに軽量化 | 厳格なREQ/ART体系の全面導入 |
| NFR/security | Iron-Ham/Trail of Bits/official | 後続Skillとして分離 | MVPに全部混ぜる |
| Distribution | official plugin + AIWG idea | 最初はClaude/Codex向けplain files | hooks/daemonの初期導入 |
