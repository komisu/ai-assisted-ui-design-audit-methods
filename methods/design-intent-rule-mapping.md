# Design Intent ↔ Rule Mapping

## 0. Document Control

| Item | Value |
|---|---|
| Document | Design Intent ↔ Rule Mapping |
| Version | v0.1 |
| Status | Experimental |
| Purpose | Design.md のRuleをDesign Intentへ対応付け、ComplianceとUX Outcome Measurementの責務を分離したまま接続する |
| Primary Output | プロダクト固有の `design-intent-rule-mapping.md` |

---

## 1. Purpose

Design.md のRuleが「何を守らせるか」だけでなく、**どのDesign Intentを実現するためのRuleか**を明示する。

このMappingは、Rule ComplianceをUX Scoreへ変換するものではない。

```text
Design Intent
      │
      ▼
Rule Mapping
      │
      ├──────────────► Compliance
      │                「Ruleを守ったか」
      │
      ▼
Outcome Measurement
「IntentがUI上で実現したか」
```

---

## 2. Why This Mapping Exists

Design.md Complianceが高くても、重要なDesign IntentがDesign.mdに十分外部化されていなければ、UX Outcomeが改善するとは限らない。

逆に、UI品質が改善していても、その改善がDesign.mdに定義されたIntentと接続できなければ、Design.mdによる効果としては扱えない。

そのため、少なくとも次の3つを分離する。

1. **Intent Coverage** — 必要なDesign Intentがどこまで外部化されているか
2. **Compliance** — 外部化されたRuleを実装がどこまで守ったか
3. **UX Outcome** — IntentがUI上の観察可能な結果としてどこまで実現したか

---

## 3. Example Intent Registry

以下はB2B SaaS／業務システムの検証で使用できる**例示的なIntent Set**である。
すべてのプロダクトにそのまま適用する標準UX分類ではない。
対象Design.mdから抽出・確認し、必要に応じてプロダクト固有のIntentを追加・変更する。

| Intent ID | Design Intent | Outcome Metric Candidate |
|---|---|---|
| INT-01 | 役割に応じて適切なUIパターンを使う | Role-to-Pattern Consistency |
| INT-02 | 重要なものだけを強く見せる | Visual Priority Accuracy |
| INT-03 | 同じ意味・指標を同じ表現にする | Semantic Consistency |
| INT-04 | StateとActionを明確に区別する | State / Action Distinction |
| INT-05 | 同じ役割のComponentを一貫させる | Component Consistency |
| INT-06 | 主作業を止めず補助情報を参照できる | Reference Pattern Appropriateness |
| INT-07 | 情報密度を保ちながら階層を明確にする | Information Hierarchy Consistency |
| INT-08 | 状態・操作を視覚だけに依存させない | Accessible State Coverage |

---

## 4. Classification

各Active Ruleについて、Intentとの関係を次のいずれかに分類する。

| Classification | Definition | Outcome Measurement |
|---|---|---|
| Primary | Intentを直接実現するRule | Opportunity抽出・Outcome測定の中心候補 |
| Supporting | Intentの実現品質を支えるRule | 原則は補助Evidence |
| Directなし | UI基盤・実装安定性等を担保するRule | Compliance / Implementation Qualityで扱い、UX Outcomeへ直接算入しない |

### Important

**すべてのRuleを無理にUX Intentへ紐づけない。**

たとえばRadius Tokenや内部実装上のLayer管理がRuleとして重要でも、
それだけでユーザー向けUX Outcomeが改善したとは言えない場合は `Directなし` とする。

---

## 5. Mapping Procedure

### Step 1 — Fix the Rule Population

Rule Registry等から対象VersionのActive Ruleを確定する。

- Deprecatedは通常対象外
- Rule IDはVersionをまたいで追跡可能にする
- Rule本文をMapping作業の都合で変更しない

### Step 2 — Extract Design Intent

Design.mdに明示された判断原則・Component選択理由・State表現・情報階層・Accessibility等からIntent候補を抽出する。

明示されていないIntentを一般論だけで補完しない。

### Step 3 — Create the Intent Registry

各Intentについて最低限以下を定義する。

- Intent ID
- Intent Name
- Intent Statement
- Scope
- Observable Outcome
- Metric Candidate
- Human Confirmation

### Step 4 — Map Rules

各Ruleについて確認する。

1. このRuleはどのIntentを直接実現するか
2. 直接ではなく実現品質を支えるだけか
3. UX Outcomeではなく基盤品質として扱うべきか
4. 複数Intentへ関係するか

1 Rule → 複数Intentを許容する。

### Step 5 — Human Review

AIによるMappingは分析上の分類であり、Design.md自身がIntentを定義していない場合は推論を含む。

特に以下はHuman Reviewする。

- Primary / Supporting境界
- 複数Intent所属
- Design.md本文に根拠が薄いMapping
- プロダクト固有Intent
- Outcome Metricとの接続

### Step 6 — Freeze

Outcome Measurementへ進む前にMapping Versionを固定する。

Outcome結果を見た後で都合よくPrimary / Supportingを変更しない。
変更する場合はVersionを上げ、必要な比較を再実行する。

---

## 6. Recommended Mapping Table

| Rule ID | Category | Rule Summary | Classification | Intent ID | Relationship Reason | Outcome Candidate |
|---|---|---|---|---|---|---|
| EXAMPLE-001 | Component | 同じRoleでは同じ選択Componentを使用する | Primary | INT-01, INT-05 | Role選択とComponent一貫性を直接規定 | Role-to-Pattern / Component Consistency |
| EXAMPLE-002 | Foundation | Radius scaleを限定する | Directなし | — | System consistencyには寄与するが単独でUX Outcomeとしない | — |
| EXAMPLE-003 | Accessibility | 選択状態を色だけで伝えない | Primary | INT-08 | 状態認識を視覚色のみに依存させない | Accessible State Coverage |

公開例では架空のRule IDを使用する。
実プロダクトのRule本文・顧客データ・監査結果は含めない。

---

## 7. Relationship to Opportunity Inventory

Rule Mappingの次に、対象画面固有のOpportunityを抽出する。

```text
Design.md
   │
   ▼
Design Intent ↔ Rule Mapping
   │
   ▼
UX Outcome Measurement Definition
   │
   ▼
Opportunity Inventory Process
   │
   ▼
Opportunity Inventory
   │
 Freeze
   │
Before / After Scoring
```

重要なのは、

**Rule = Opportunity ではない。**

複数Ruleが1つのユーザーRoleを支えることがあるため、
Outcomeの分母にはRule数ではなく、Measurement Definitionで定義したObservation Opportunityを使用する。

---

## 8. AI Execution Guidance

AIにMappingを依頼する場合は以下を守る。

- Source of truthを明示する
- Active Ruleを漏れなく確認する
- Rule本文を勝手に変更しない
- Intentを一般論で補完しない
- Primary / Supporting / Directなしを区別する
- 複数Intent所属を許容する
- 根拠をRelationship Reasonに記録する
- 判断不能なものはHuman Confirmation Requiredとする
- Mapping結果をCompliance ScoreやUX Scoreへ変換しない
- Human Review前にFrozenにしない

---

## 9. Exit Criteria

- [ ] 対象VersionのActive Rule母集団が固定されている
- [ ] Intent Registryが定義されている
- [ ] 全Active RuleがPrimary / Supporting / Directなしのいずれかに分類されている
- [ ] Intentに紐づくRuleにRelationship Reasonがある
- [ ] 複数Intent所属が確認されている
- [ ] Directなしを無理にOutcomeへ接続していない
- [ ] HCRが明示されている
- [ ] Human Reviewが完了している
- [ ] Mapping VersionがFreezeされている

---

## 10. Scope Boundary

このMethodはDesign IntentとRuleの**Traceability**を作るためのものである。

以下は別工程で扱う。

- Ruleが対象画面に必要か → Rule Applicability
- AfterがRuleを守ったか → Design.md Compliance Verification
- UI／実装品質がどう変化したか → UI Audit Process
- 対象画面で何をOutcomeの分母にするか → UX Outcome Opportunity Inventory Process
- 実際のBefore / After Outcome → Outcome Scoring

Compliance、UI Quality、UX Outcomeを同一スコアへ自動統合しない。
