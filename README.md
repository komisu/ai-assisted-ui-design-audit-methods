# AI-Assisted UI Design & Audit Methods

AIを用いたUI設計・実装において、**設計判断を外部化し、実装結果をEvidenceベースで検証するためのMethods集**です。

このRepositoryでは、B2B
SaaS／業務システムを主な対象として、既存UIの調査、Design.mdのAI可読性確認、Rule
ID管理、Rule準拠検証、UI品質監査、UX
Outcome測定のためのOpportunity抽出などを扱います。

各Methodは同じ品質を測るものではありません。

**Ruleを守ったか、UI／実装品質がどう変わったか、Design
IntentがUI上のどこで評価可能かを分離して扱う**ことを重視しています。

> Compliance is not quality.\
> Rule compliance, UI quality, and UX outcome are related, but they are
> not the same thing.

------------------------------------------------------------------------

## Status

このRepositoryは実践・検証を通じて更新中です。

### Current Methods

  ------------------------------------------------------------------------------------------------------------------------------------------
  Document                                             Status                  Purpose
  ---------------------------------------------------- ----------------------- -------------------------------------------------------------
  `ui-audit-process.md`                           Tested / evolving       単一UIの現状監査、および同一要件のBefore / After比較監査

  `design-md-ai-readability-audit.md`        Tested / evolving       Design.mdがAIにとって一貫した実装仕様として機能するかを監査

  `design-md-rule-id-instructions.md`                  Tested / evolving       Design.md内の検証可能なRuleへ永続Rule IDを付与・管理

  `design-md-compliance-verification.md`          Tested / evolving       Design.mdを参照したUI／実装をRule ID単位で検証

  `ux-outcome-opportunity-inventory-process.md`   **Experimental**        UX Outcome採点前に、対象UI上のObservation
                                                                               Opportunityを抽出・固定
  ------------------------------------------------------------------------------------------------------------------------------------------

`Experimental`
は、方法論として未完成という意味ではなく、実運用を通じて入力・判定境界・後工程との接続方法が変更される可能性があることを示します。

------------------------------------------------------------------------


## Repository Structure

```text
.
├── README.md
└── methods/
    ├── ui-audit-process.md
    ├── design-md-ai-readability-audit.md
    ├── design-md-rule-id-instructions.md
    ├── design-md-compliance-verification.md
    └── ux-outcome-opportunity-inventory-process.md
```

ファイル名はVersionに依存しない固定名とし、最新版を同じPathで参照できるようにします。

### Versioning Convention

- **Filename**：固定。Version更新時も変更しない
- **Document Version**：各Method本文のDocument Control／Version表記で管理
- **Git History**：Methodの変更履歴を管理
- **Git Tag / GitHub Release**：Repository全体の公開Versionを管理

これにより、AIエージェントや利用者はVersion更新のたびに参照Pathを変更せず、常に同じファイル名から最新版を利用できます。

## Core Principles

このRepositoryでは、次の原則を共通して使用します。

-   **Observation before judgment** ---
    改善判断の前に、まず現状を事実として把握する
-   **Evidence before inference** ---
    Evidenceのない意図・原因・重要度を推測しない
-   **Separate responsibilities** --- Compliance、UI Quality、UX
    Outcomeを混同しない
-   **Human decision remains explicit** ---
    設計判断や曖昧な事項をAIに勝手に確定させない
-   **Freeze before comparison** --- Before /
    Afterを評価する場合、Scope・基準・分母を結果を見る前に固定する
-   **Traceability** --- Rule ID、Stable Key、Opportunity
    ID等を使い、Versionをまたいで追跡可能にする

------------------------------------------------------------------------

## Method Map

``` text
Existing Product / UI
        │
        ▼
UI-AUDIT-PROCESS
        │
        ├─ Baseline Discovery
        ├─ UI Inventory
        ├─ Pattern / Divergence Analysis
        ├─ UX / Accessibility Evaluation
        └─ Human Decision
        │
        ▼
Design System / Design.md / Components / Reference
        │
        ├──────────────────────────────┐
        ▼                              ▼
AI Readability Audit            Rule ID Management
        │                              │
        └──────────────┬───────────────┘
                       ▼
                 AI Implementation
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
      Compliance Verification   UI Audit
      「Ruleを守ったか？」       「UI／実装品質は
                                 どう変化したか？」
             │                   │
             └─────────┬─────────┘
                       │
                       ▼
              Human Review / Feedback
                       │
                       ▼
          Design.md / System / Process改善
```

UX Outcomeを扱う場合は、これとは別の評価系統を追加します。

``` text
Design Intent
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
   Freeze Gate
      │
  ┌───┴───┐
  ▼       ▼
Before   After
  │       │
  └───┬───┘
      ▼
Outcome Delta
```

このUX Outcome系統は現在Experimentalです。

------------------------------------------------------------------------

## 1. UI Audit Process

既存フロントエンドを事実ベースで把握し、UI／実装そのものの状態・品質・一貫性・構造を監査するための全体プロセスです。

### Audit Modes

**Mode A --- Single Audit**

単一のUI／プロダクトを対象に、現状把握からSystemize、Verificationまでを扱います。

**Mode B --- Before / After Comparative Audit**

同一Requirements・Scopeを固定し、BeforeとAfterを同じ基準で独立監査した後、Findingを対応付けます。

Comparison Status:

-   `Resolved`
-   `Improved`
-   `Unchanged`
-   `Regressed`
-   `Newly Detected`
-   `Not Comparable`

Design.mdを使用したという事実だけでAfterを高評価しません。また、見た目の改善とComponent
reuse／CSS構造などの内部実装改善は分離して記録します。

### Responsibility Boundary

UI AuditではDesign.mdのRule IDごとのPass / Fail判定を行いません。

``` text
Spacingの一貫性が改善した
→ UI Audit

Rule ID XXX-001を遵守した
→ Design.md Compliance Verification
```

------------------------------------------------------------------------

## 2. Design.md AI Readability Audit

作成済みのDesign.mdが、AIエージェントにとって実装仕様として機能する状態かを監査します。

主な確認対象:

-   Ruleの発見しやすさ
-   重複・分散
-   値／使用ルール／判断原則の区別
-   矛盾・条件不足
-   Layout／Component／Accessibilityの実装判断可能性
-   AIが独自の値・Variant・Component・Layoutを生成するリスク

不足を発見してもAIに新しい仕様を作らせず、`Documentation Gap`、`Existing Implementation Investigation Required`、`Human Decision Required`、`Out of Scope`
等として分類します。

------------------------------------------------------------------------

## 3. Design.md Rule ID Instructions

Design.md内の検証可能なRuleへ、Versionをまたいで追跡できる永続Rule
IDを付与・管理します。

Rule IDは章番号や表示順から独立させます。

主な原則:

-   一度発行したIDは原則変更・再利用しない
-   新規Ruleには新規IDを発行する
-   廃止IDはDeprecatedとして保持する
-   意味が変わらない文言修正では既存IDを維持する
-   分割・統合時は旧IDとの対応関係をRule Registryへ残す
-   判断不能な変更は `Human Confirmation Required`

------------------------------------------------------------------------

## 4. Design.md Compliance Verification

Design.mdを参照して生成・実装されたUIが、Design.mdのRuleをどの程度遵守しているかをRule
ID単位で検証します。

Applicabilityを先に確認し、ApplicableなRuleのみComplianceの母数に含めます。

### Compliance Result

-   `Pass`
-   `Partial`
-   `Fail`
-   `Not Defined`
-   `Human Confirmation Required`

`Not Applicable` はCompliance Rateの母数に含めません。

### Root Cause

Fail／Partial等は、Evidenceがある範囲で次のように分類します。

-   `Implementation Failure`
-   `Documentation Ambiguity`
-   `Documentation Gap`
-   `Rule Conflict`
-   `Evidence Insufficient`
-   `Human Decision Required`

Compliance結果はUI品質やUX Outcomeへ自動変換しません。

------------------------------------------------------------------------

## 5. UX Outcome Opportunity Inventory Process

> **Status: Experimental**

Design Intentが対象UI上のどこで評価可能かを特定し、Before /
AfterのOutcomeを採点する**前に**Observation
Opportunityを抽出・固定するプロセスです。

目的は、Afterを見てから都合のよい評価項目を追加したり、実装要素数によって評価の分母が変化したりすることを防ぐことです。

### Core Principle

**Opportunity is not a Rule.**

Opportunityとは、

> ユーザーが対象画面で特定のDesign
> Intentの恩恵を受けるべき「役割・意味・判断の単位」

です。

Rule数をUX Outcomeの分母にはしません。

### Required Inputs

原則として以下を使用します。

1.  Target UI Source
2.  Design Intent ↔ Rule Mapping
3.  UX Outcome Measurement Definition

必要に応じて、Rule Applicability、Requirements / Comparison
Contract、Stable Key / Screen Structureを追加します。

### Freeze Gate

Opportunity Inventoryは人間が確認した後に `Frozen` とします。

Freeze後はBefore /
Afterの結果を見てOpportunityを追加・削除しません。変更が必要な場合はInventoryのVersionを上げ、Change
Logを残し、Before / After双方を再評価します。

この工程ではSuccess / Failure、UX Score、Complianceを判定しません。

------------------------------------------------------------------------

## Responsibility Model

このMethods群では、AIと人間の役割を意図的に分離します。

### AI

-   Source／Repositoryの調査
-   Evidence収集
-   Inventory作成
-   Pattern／Divergence抽出
-   Rule Applicability確認
-   Compliance Verification
-   Opportunity Candidate抽出
-   Human Confirmation Requiredの明示

### Human

-   設計意図の確定
-   KEEP／MERGE／VARIANT／REMOVE／REVIEW等の判断
-   新しい仕様・Ruleの決定
-   HCRの解消
-   Freeze承認
-   最終的な品質判断
-   Method自体の改善判断

AIがEvidenceなしに意図や仕様を補完することを前提にしません。

------------------------------------------------------------------------

## Recommended Usage

すべての文書を毎回一括でAIへ渡す必要はありません。

目的に応じて必要なMethodだけを使用します。

``` text
既存UIを調査したい
→ UI-AUDIT-PROCESS

Design.md自体を確認したい
→ AI Readability Audit

Complianceを測定可能にしたい
→ Rule ID Instructions

生成UIがDesign.mdを守ったか確認したい
→ Compliance Verification

UI／実装品質のBefore / Afterを比較したい
→ UI-AUDIT-PROCESS / Comparative Mode

Design IntentのUX Outcome測定機会を固定したい
→ UX Outcome Opportunity Inventory Process
```

------------------------------------------------------------------------

## Versioning

各文書は独立してVersion管理します。

Methodの変更によって過去の検証結果との比較可能性が変わる場合は、Versionを固定して再現可能性を維持してください。

旧版を残す場合は、通常利用する最新版と混同しないよう明示的にArchiveしてください。

------------------------------------------------------------------------

## Experimental Work

現在、以下の領域を継続的に検証しています。

-   Design Intentの外部化
-   Design Intent ↔ Rule Mapping
-   UX Outcome Measurement
-   Intent Achievement Evaluation
-   AI実装におけるRule ComplianceとUI Qualityの関係
-   Verification結果をDesign System／Design.md／Referenceへ戻すFeedback
    Loop
-   AIが判断すべき領域とHuman Judgmentとして残す領域の境界

これらは実践・検証を通じて更新します。

------------------------------------------------------------------------

## Scope

このRepositoryは、特定のプロダクト、企業、Design
System、AIエージェントに依存することを意図していません。

具体的なDesign.md、Source
Code、顧客データ、プロダクト固有の監査結果は含みません。

公開例を使用する場合は、特定の実プロダクトに依存しない架空・一般化した例を使用します。

------------------------------------------------------------------------

## Disclaimer

このRepositoryのMethodsは、AIによる自動判定を最終的なデザイン判断の代替とするものではありません。

実際のプロダクトで利用する場合は、対象プロダクトのRequirements、ユーザー、業務文脈、アクセシビリティ要件、技術制約等を確認し、必要なHuman
Reviewを行ってください。
