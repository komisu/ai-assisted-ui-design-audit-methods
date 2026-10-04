# UX Outcome Opportunity Inventory Process v0.1

## 0. Document Control

  -----------------------------------------------------------------------------------------
  Item                      Value
  ------------------------- ---------------------------------------------------------------
  Document                  UX Outcome Opportunity Inventory Process

  Version                   v0.1

  Purpose                   UX Outcome Measurement
                            の採点前に、対象UI上の測定機会（Opportunity）を抽出・固定する

  Position                  Design Intent Mapping と Outcome Scoring の間

  Primary Output            `opportunity-inventory_<target>.md`

  Scoring                   **この工程では実施しない**
  -----------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 1. Purpose

本プロセスは、Design System / Design.md に外部化された Design Intent
が、 対象UI上のどこで評価可能かを特定し、Before / After 比較に使用する
**Observation
Opportunity（適用機会）を採点前に固定するための標準プロセス**である。

目的は、Afterを見てから都合のよい評価項目を追加したり、
実装されている要素の数によって分母が変わったりすることを防ぐことにある。

本工程ではUIの良否、Before / Afterの優劣、Compliance Pass /
Failを判定しない。

------------------------------------------------------------------------

# 2. Position in the Verification Flow

``` text
Design.md
   │
   ├─ Rule Applicability
   │      └─ 対象画面に必要なRuleを固定
   │
   ├─ Design Intent ↔ Rule Mapping
   │      └─ Ruleが何のIntentを実現するかを定義
   │
   └─ UX Outcome Measurement Definition
          └─ Intentを何を単位として測るか定義
                    │
                    ▼
        ★ Opportunity Inventory Process
                    │
                    ▼
          Opportunity Inventory
                    │
                 Freeze Gate
                    │
              ┌─────┴─────┐
              ▼           ▼
            Before       After
              │           │
              └─────┬─────┘
                    ▼
              Outcome Delta
```

Compliance Verificationとは別系統である。

-   Compliance Verification = AfterがDesign.mdのRuleを守ったか
-   UX Outcome Measurement = Design IntentがUI品質としてどう現れたか

------------------------------------------------------------------------

# 3. Required Inputs

原則として以下を入力する。

## 3.1 Required

1.  **Target UI Source**
    -   HTML / source code / executable UI
    -   対象画面・Flowを特定できるもの
2.  **Design Intent ↔ Rule Mapping**
    -   Intent ID
    -   Rule ID
    -   Primary / Supporting / Directなし
    -   Intentの意味
3.  **UX Outcome Measurement Definition**
    -   IntentごとのObservation Unit
    -   Success Condition
    -   Exclusion
    -   Metric definition

## 3.2 Strongly Recommended

4.  **Rule Applicability**
    -   Applicable
    -   Conditional
    -   Not Applicable
    -   判定根拠
5.  **Requirements / Comparison Contract**
    -   対象画面
    -   対象Flow
    -   Before / Afterで維持すべき機能
    -   Requirement Delta
    -   Comparison Constraint
6.  **Stable Key / Screen Structure**
    -   既存Audit等で画面領域の対応が整理済みの場合は利用する

------------------------------------------------------------------------

# 4. Source of Truth Priority

矛盾がある場合、以下の優先順位で扱う。

1.  明示された Requirements / Comparison Contract
2.  UX Outcome Measurement Definition
3.  Design Intent ↔ Rule Mapping
4.  Rule Applicability
5.  Design.md
6.  Target UI Source

Target UI Sourceに存在しないという理由だけで、
Requirements上必要なOpportunityを削除してはならない。

逆に、Target UI SourceにAfter独自の実装が存在するという理由だけで、
Opportunityを追加してはならない。

不一致は `Human Confirmation Required` または
`Requirement Delta Candidate` として記録する。

------------------------------------------------------------------------

# 5. Core Principle --- Opportunity Is Not a Rule

Rule数をOutcomeの分母にしてはならない。

Opportunityとは、

> ユーザーが対象画面で特定のDesign
> Intentの恩恵を受けるべき「役割・意味・判断の単位」

である。

例：

-   同じ「日付範囲選択」というRoleにRuleが5つ関係していても、Opportunityは原則1件
-   同じ情報項目がChart / Table / Summary / Legendの4箇所に存在しても、
    Semantic Consistencyではその情報項目の横断セットを1
    Opportunityとして扱う
-   同じButtonが10個存在しても、Component ConsistencyではRole /
    Component family単位で集約できる

------------------------------------------------------------------------

# 6. Prohibited Actions

本工程では以下を行ってはならない。

1.  Before / Afterの優劣判定
2.  Success / Failure採点
3.  Compliance Pass / Partial / Fail判定
4.  UX Score算出
5.  Afterの実装を見て評価項目を追加する
6.  Rule数をOutcome分母として使用する
7.  class名 / id名だけでStable Keyを定義する
8.  同じRoleを実装方式の違いだけで別Opportunityにする
9.  存在しない実装を理由にRequirements上のOpportunityを削除する
10. Design.mdにない一般UX原則を無断でOutcome条件へ追加する
11. Source内コメントをDesign IntentのEvidenceとして扱う
12. 不明事項を推測で確定する

------------------------------------------------------------------------

> **Public example note:**
> 以下の例は、特定のプロダクトや業務ドメインに依存しない架空のUI例を使用する。

# 7. Analysis Procedure

## Step 1 --- Freeze Inputs

使用する各ファイルについて以下を記録する。

-   filename
-   version
-   date（存在する場合）
-   target
-   source snapshot / hash（取得可能な場合）

分析開始後にInputが変更された場合はInventoryをFreezeしない。

------------------------------------------------------------------------

## Step 2 --- Identify Target Scope

Requirements / Comparison Contractから以下を抽出する。

-   Screen
-   Tab
-   Flow
-   Region
-   User operation
-   Supplemental information
-   Data visualization
-   Table / Form / Selection operation

対象外領域を明示する。

------------------------------------------------------------------------

## Step 3 --- Build Stable Structure

対象画面をRoleベースで分解する。

推奨Stable Key:

`{Screen or Tab}/{Region}/{Role}`

例:

``` text
Common/Navigation/ViewSwitch
Common/Help/ReferenceHelp
Dashboard/DateRange/PeriodSelector
Analytics/ViewMode/DisplaySelector
```

### Stable Keyに使用してよいもの

-   User-facing role
-   Information role
-   Region role
-   Functional role
-   Screen / Tab context

### Stable Keyに依存してはいけないもの

-   class
-   id
-   generated component name
-   DOM orderだけ
-   Before / After固有の実装名

これらはEvidenceとして別列に記録する。

------------------------------------------------------------------------

## Step 4 --- Extract Candidate Opportunities by Intent

Measurement Definitionの各Intentについて、 Observation
Unitに該当するRoleをTarget UI / Requirementsから抽出する。

各Candidateについて以下を確認する。

1.  Observation Unitの定義に合うか
2.  対応するPrimary Ruleがあるか
3.  Rule ApplicabilityがApplicableか
4.  Conditionalの場合、条件がRequirements上成立するか
5.  同じRoleを重複計上していないか
6.  Before / Afterで同じStable Keyとして比較可能か

------------------------------------------------------------------------

## Step 5 --- Deduplicate

同じIntent内で同じRoleを二重計上しない。

### Deduplication Examples

#### Role-to-Pattern

日付範囲選択に関連するRuleが複数あっても、

``` text
Dashboard/DateRange/PeriodSelector
```

を1 Opportunityとする。

#### Semantic Consistency

「Metric A」が

-   Tile
-   Chart
-   Table
-   Legend

に存在する場合、

``` text
Semantic Entity = Metric A
Surfaces = Tile / Chart / Table / Legend
```

として1 Opportunityとする。

#### Component Consistency

同じRoleのButtonが複数存在する場合、 Component
family単位で評価するかRole単位で評価するかは Measurement
Definitionに従う。

------------------------------------------------------------------------

# 8. Conditional Rules

`Conditional` Ruleは自動的にOpportunityへ含めない。

以下を判定する。

### Condition Established

Requirements / target content上、条件が必ず成立する。

→ Opportunityへ含める。

### Condition Not Established

実装方式によってのみ成立する。

→ 原則、分母へ入れない。

### Unclear

Requirementsだけでは判断不能。

→ `HCR` とする。

Afterに実装されているからという理由だけで
Conditionalを成立扱いしてはならない。

------------------------------------------------------------------------

# 9. Before / After Handling

## 9.1 Preferred Method

Comparisonの場合は、可能ならRequirementsまたはBeforeの内容・機能を基準に
Candidate Opportunityを先に抽出する。

Afterは、

-   Stable Key対応確認
-   Requirement Delta確認
-   Evidence location確認

のために使用する。

## 9.2 Different Component, Same Role

例：

``` text
Before: Segmented Control
After : Select
Role  : PeriodSelector
```

これは同一Opportunityである。

Componentの選択が適切かどうかは後工程のScoringで判定する。

## 9.3 After-only Feature

Afterにのみ存在する要素は自動的にOpportunityへ追加しない。

以下のいずれかに分類する。

-   Requirement-preserving implementation
-   Requirement Delta Candidate
-   Accessibility implementation
-   Structural implementation
-   HCR

------------------------------------------------------------------------

# 10. Opportunity ID

Opportunityには永続IDを付与する。

推奨形式:

`OPP-{Intent ID}-{3桁連番}`

例:

``` text
OPP-INT01-001
OPP-INT03-004
OPP-INT08-012
```

同じOpportunityを後続検証でも追跡できるよう、
採点結果が変わってもIDを変更しない。

Opportunityの意味が変わった場合は新IDを発行し、
旧IDをDeprecatedとして履歴に残す。

------------------------------------------------------------------------

# 11. Required Output

成果物:

`opportunity-inventory_<target>.md`

## 11.1 Header

  Field                     Value
  ------------------------- -------------------------
  Target                    
  Design.md                 
  Intent Mapping            
  Measurement Definition    
  Applicability             
  Requirements / Contract   
  Inventory Status          Draft / Review / Frozen
  Created                   

------------------------------------------------------------------------

## 11.2 Summary

  Intent     Candidate   Included   HCR   Excluded
  -------- ----------- ---------- ----- ----------
  INT-01                                
  INT-02                                
  INT-03                                
  INT-04                                
  INT-05                                
  INT-06                                
  INT-07                                
  INT-08                                

------------------------------------------------------------------------

## 11.3 Opportunity Inventory

  ---------------------------------------------------------------------------------------------------------
  Opportunity Intent Stable Role / Observation Primary Applicability Inclusion Inclusion Before After HCR
  ID Key Semantic Unit Rule IDs Reason Evidence Evidence
  Entity
  ---------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

### Inclusion

Allowed values:

-   Included
-   Excluded
-   HCR
-   Not Comparable

**この工程ではSuccess / Failure列を作らない。**

------------------------------------------------------------------------

## 11.4 Conditional Review

Rule ID Condition Established? Evidence Decision --------- -----------
-------------- ---------- ----------

------------------------------------------------------------------------

## 11.5 Deduplication Log

重複候補を統合した場合は必ず残す。

  ---------------------------------------------------
  Candidate A Candidate B Decision Reason Resulting
  Opportunity ID
  ---------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

## 11.6 Requirement Delta Candidates

  ----------------------------------------------------
  ID Stable Key Observed Why It May Be Treatment HCR
  Difference Requirement
  Delta
  ----------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

# 12. Evidence Rules

Evidenceは可能な限り以下を記録する。

-   filename
-   line / selector / DOM path
-   visible label
-   role
-   runtime state
-   screenshot reference（利用可能な場合）

Evidenceの優先度:

1.  Runtime / rendered behavior
2.  DOM / semantic structure
3.  Source implementation
4.  Visual evidence

Source内コメントだけをEvidenceとして使用しない。

推測はEvidenceとして扱わない。

------------------------------------------------------------------------

# 13. Human Confirmation Required

以下はHCRとする。

-   Roleの意味がRequirementsから判断できない
-   同じRoleか別Roleか判断できない
-   Conditional成立条件が不明
-   Requirement Deltaの可能性がある
-   Design Intent Mappingと実画面の意味が衝突する
-   Success Conditionを適用するには業務知識が必要

HCRは勝手に解消しない。

------------------------------------------------------------------------

# 14. Freeze Gate

Opportunity InventoryをOutcome Scoringへ渡す前に以下を確認する。

-   [ ] 対象Scopeが固定されている
-   [ ] 全Intentを確認した
-   [ ] Candidate Opportunityを全件確認した
-   [ ] 同一Intent内の重複を除去した
-   [ ] Conditional Ruleを確認した
-   [ ] Requirement Delta Candidateを確認した
-   [ ] HCRを人間が確認した
-   [ ] Opportunity IDを固定した
-   [ ] Stable Keyを固定した
-   [ ] Included Opportunity数をIntent別に固定した
-   [ ] Inventory Statusを `Frozen` にした

**Freeze後はBefore /
Afterの結果を見てOpportunityを追加・削除してはならない。**

変更が必要な場合:

1.  Inventoryのversionを上げる
2.  Change Logを残す
3.  Before / Afterを両方再採点する

------------------------------------------------------------------------

# 15. Exit Criteria

本工程は以下を満たした時点で完了とする。

1.  対象Scope内のOpportunityが全Intentについて抽出済み
2.  Included / Excluded / HCRが全Candidateに付与済み
3.  Conditionalの扱いが記録済み
4.  重複統合が記録済み
5.  Stable KeyとOpportunity IDが固定済み
6.  HCRが解消または明示的に保留済み
7.  人間がInventoryをレビュー済み
8.  Inventory Status = `Frozen`

------------------------------------------------------------------------

# 16. AI Execution Instruction

あなたは **Opportunity Inventory Analyst** として作業する。

目的は、UX Outcomeを採点することではなく、
採点対象となるOpportunityを漏れなく、重複なく抽出することである。

必ず以下を守る。

-   UIを改善しない
-   Sourceを変更しない
-   Before / Afterの優劣を判定しない
-   Success / Failureを判定しない
-   UX Scoreを計算しない
-   Complianceを再判定しない
-   Afterの実装を基準にOpportunityを増やさない
-   Rule数を分母にしない
-   不明点を推測しない
-   HCRを明示する
-   Evidenceを付ける
-   Measurement DefinitionのObservation Unitを厳守する
-   Design Intent MappingのPrimary Ruleを中心にOpportunityを抽出する

最終成果物として `opportunity-inventory_<target>.md` のみを作成する。

Freezeは人間の確認後に行う。 AI単独で `Frozen` にしてはならない。

------------------------------------------------------------------------

# 17. Standard Service Use

本プロセスは特定プロダクト専用ではない。

案件ごとに変わるもの:

-   Design.md
-   Intent Mapping
-   Measurement Definition
-   Applicability
-   Requirements
-   Target UI
-   Opportunity Inventory

変えないもの:

-   Opportunity抽出の考え方
-   Stable Key原則
-   Deduplication原則
-   Conditionalの扱い
-   Evidence原則
-   HCR
-   Freeze Gate
-   採点前に分母を固定する原則

これにより、異なるクライアントでも

`Intent → Opportunity → Before / After Outcome`

を同じ監査構造で実行できる。
