# UI Audit Process

**Version:** 0.6
**Purpose:** B2B SaaS / 業務システムのフロントエンドを事実ベースで把握し、UI Inventory・分析・人間判断・Systemize・Verificationへ段階的に進める。また、同一要件・同一対象範囲のBefore / Afterを同一基準で監査し、UIおよび実装の状態・品質・一貫性・構造の変化をEvidence付きで追跡する。

## Core Principle

UI Auditは、最初から「何を直すか」を決める工程ではない。

まず既存実装から事実を収集し、何が存在し、どのように使われ、どこに類似・分岐があるかを可視化する。その後、人間が意味・背景・UX上の妥当性を判断し、必要なものだけをSystemizeする。

基本の流れ：Inventory → Pattern → Divergence → Meaning → UX / Design → Decision → Systemize → Verification

# Audit Modes

本プロセスは、共通のPhase・監査基準・Evidence原則を用いて次の2モードを扱う。

## Mode A — Single Audit

単一のUI / Productの現状を監査する。v0.5までの標準モードを維持する。比較用の入力、Comparison Status、Comparison Reportは不要であり、各Phaseの既存成果物をそのまま使用する。

## Mode B — Before / After Comparative Audit

同一要件・同一対象範囲のBeforeとAfterを、同一のAudit Contractと監査基準でそれぞれ独立に観察した後、対応関係と差分を評価する。

- **Before:** 基準時点のUI / implementation
- **After:** 変更後のUI / implementation
- **Comparison:** BeforeとAfterの観察結果を対応付け、変化の方向・内容・根拠を評価したもの

Afterは改善版であるとは限らない。Design.mdの利用、変更後であること、見た目が整って見えることのいずれも高評価の根拠にしない。

## Mode Selection

監査開始時に`Audit Mode: Single | Comparative`を宣言する。指定がない場合はSingleとする。Comparativeでは、Phase A開始前にComparison Audit Contractを作成し、Comparison Gateを通過させる。

# Responsibility Boundary

## UI-AUDIT-PROCESS

UI / implementationそのものについて、観察可能な状態・品質・一貫性・構造と、その変化を評価する。対象にはFoundation、Component、Layout、Interaction / State、Accessibility、Responsive behavior、CSS / styling implementation、hardcoded values、duplicate implementation、reuse等を含む。

## Design.md Compliance Verification

Design.mdに記載された個々のRuleへの準拠を、Rule ID単位でPass / Fail等として評価する。

## Boundary Rules

- UI AuditはDesign.md Rule IDごとのPass / Fail表を作らない
- Design.mdは、存在・参照可能性・導入条件等のAudit ContextまたはEvidence sourceとして記録できる
- `Spacingのばらつきが減少した`、`shared component reuseが増加した`はUI Auditで扱う
- `Rule ID XXX-001を遵守した`はCompliance Verificationで扱う
- Compliance結果をUI Auditへ参照する場合は別成果物へのCross-referenceとし、UI品質のEvidenceへ自動変換しない
- Rule準拠とUI品質変化が一致しない場合、両者を独立して報告する

# Comparison Audit Contract — Mode B Only

比較可能性を後から調整しないため、監査実行前に以下を固定する。

| Contract Field | Required Record |
| --- | --- |
| Audit Scope | Repository、branch / commit / release、route、screen、flow、component、viewport等 |
| Requirements | Before / After双方に与えられた同一要件。差異があれば明示 |
| Target Screens / Flows | 対応するscreen / flowと対象外 |
| Audit Criteria | 適用するPhase、category、評価観点 |
| Evidence Requirements | source、runtime、screenshot、test等の必要水準 |
| Evaluation Granularity | route、screen、component family、implementation unit等 |
| Exclusion Conditions | 入力除外、環境制約、未実装、検証不能条件 |
| Runtime Conditions | viewport、browser、data、state、locale、permissions等 |
| Source Snapshot | Before / Afterのcommit、archive、timestamp等 |

## Contract Rules

- Before / Afterで同じ基準、判定粒度、対象範囲、Evidence requirementを使用する
- 一方にしか適用できない項目は黙って除外せず`Not Comparable`と理由を記録する
- 比較開始後に基準を変更した場合、変更内容、理由、影響対象を記録し、双方を同じ基準で再評価する
- Afterだけへ新規criteriaを追加しない。追加が必要ならBeforeも遡及評価する
- 同一要件でない場合、差分をDesign.mdや実装変更の効果として因果推定しない
- 条件差が軽微でも、比較結果へ`Comparison Constraint`として明記する

## Comparison Gate

比較開始前に各Contract Fieldを`Fixed / Partially Matched / Unmatched / Unknown`で確認する。

- 主要項目が`Fixed`なら比較を実行できる
- `Partially Matched`は影響範囲を限定し、該当項目を`Not Comparable`または制約付き比較とする
- 結果を左右する`Unmatched / Unknown`がある場合、推測で揃えず`Human Confirmation Required`とする

# Evidence Model

Evidenceは、観察と比較判断の両方を第三者が追跡できる粒度で記録する。

## Evidence Types

- **Source Evidence:** file path、line / symbol、component、selector、import / usage、configuration、test
- **Runtime Evidence:** route、viewport、data / state、操作手順、observed result、capture reference
- **Visual Evidence:** screenshot、design asset、rendered state。視覚差のみで内部実装を推定しない
- **Derived Evidence:** sourceから機械的に集計したcount、normalized value、reference map。算出方法を記録する
- **Human Evidence:** Product / Design / Engineering等による確認。確認者または役割、確認日、確認内容を記録する

各Findingには、少なくともObservation、対象、Evidence reference、Evidence type、verification stateを含める。行番号が不安定な場合はfile pathに加えてsymbol / selector / structural locatorを記録する。

# Finding Identity and Traceability

## Finding ID

既存成果物のFinding IDは変更しない。新規Findingは、監査内で一意かつ再実行時に可能な限り再利用できるIDを付与する。

推奨形式：`F-{AREA}-{NNN}`（例：`F-COMP-014`、`F-A11Y-006`）。連番自体にseverityやstatusの意味を持たせない。

## Stable Key

Finding IDとは別に、同じ問題を再監査で照合するための`Stable Key`を保持する。

推奨構成：

`{area}::{target-role-or-family}::{structural-locator}::{issue-signature}`

例：`component::primary-action::orders/header-actions::duplicate-direct-implementation`

- pathや行番号だけをStable Keyにしない
- 表示文言だけをStable Keyにしない
- route / semantic role / component family / structural locator / issue signature等、変更後も意味が残る要素を使う
- renameやfile移動時はStable Keyを維持し、Evidence locatorのみ更新する
- 問題の意味または対象が本質的に変わった場合は別Stable Keyとする

## Comparison Cross-reference

Comparison Recordは次を持つ。

- Comparison ID: `C-{AREA}-{NNN}`
- Stable Key
- Before Finding ID(s)
- After Finding ID(s)
- Before Observation / Evidence
- After Observation / Evidence
- Comparison Status
- Change Rationale
- Confidence / Verification State
- Human Confirmation Required

1対1に限定しない。統合により複数Before findingsが1つのAfter findingへ収束した場合や、1つの実装が複数へ分岐した場合は、1対多 / 多対1のmappingを明示する。対応付けできないものを無理に同一Findingとしない。

# Audit Rules for AI

## Do
- Repository / supplied source filesを調査する
- 観測できる事実と推測を分離する
- 可能な限りEvidenceとしてfile path / file / component / selector等を示す
- 類似UIは「Candidate」として記録する
- Third-party UIとCustom UIを区別する
- ソースから判断できない事項は `Unknown / Human Confirmation Required` とする
- 指定されたPhaseの範囲を守る
- Human Decisionが必要な事項を勝手に確定しない
- Comparative AuditではBefore / Afterを同じContractで独立に観察してから対応付ける
- 差が確認できない場合は`Unchanged`、悪化した場合は`Regressed`と記録する
- 比較不能またはEvidence不足は`Not Comparable`または`Human Confirmation Required`とする

## Do Not
- Audit中にcode / CSS / componentを変更しない
- 改善・統一・refactoringを行わない
- Design Token / Design.md / Storybookを新規作成しない
- UIの種類が多いこと自体を「悪い」と判定しない
- 類似していることだけを理由に統合を決定しない
- 既存実装の意図を推測しない
- 既存ルールが正しいと仮定しない
- Evidenceのない割合・利用頻度・重要度を推定しない
- Afterを「改善されているはず」と仮定しない
- Design.mdを使用した事実だけでAfterを高評価しない
- Afterの結果を見てBeforeの基準・粒度・対象範囲を変更しない
- 視覚差からToken reuse、Component reuse、内部構造、Accessibility実装を推測しない
- UI Audit内でDesign.md Rule IDごとのCompliance判定を重複実施しない

# Phase A — Baseline Discovery
目的：改善判断を行う前に、監査対象とFrontend / Styling / UI Asset / Component構造の現状を事実として把握する。

## 01 Scope
Inspect: Product / application、Repository、Branch / commit / release / version、対象Route / Page / Component / Style、Design / visual assets、Documentation、Out of Scope、Known constraints、Unknowns。

Output: Audit Target、Out of Scope、Known Constraints、`Unknown / Human Confirmation Required`。

Comparative Auditでは、Before / After双方について同じ項目を記録し、Comparison Audit Contract、Source Snapshot、Runtime Conditions、条件差、Comparison Gate結果を追加する。片側でのみ利用可能なassetやtestは、比較Evidenceとして自動採用せず可用性差を記録する。

## 02 Frontend Architecture
Inspect: Framework/version、routing、rendering、build tool、package manager、runtime、monorepo、iframe/micro frontend、TypeScript/JavaScript/HTML/PHP/Blade/ERB/jQuery/SVG等、App/Global・Layout・Shared UI・Domain・Page-local・Direct HTML・Utility、browser/device/responsive/deployment constraints。

名称やdirectoryだけから再利用意図を確定しない。

Output: Frontend Architecture Summary、Component Architecture Summary、Environment Summary、Evidence、Human Confirmation Required。

## 03 Styling Architecture
Inspect: Global CSS、CSS Modules、SCSS/Sass、CSS-in-JS、Tailwind、inline style、SVG attributes、UI theme、style locations、CSS variables/theme/token存在、hardcoded color/spacing/type/radius/border/shadow/size/breakpoint、override/specificity/dependencies、similar style candidates、hover/focus/active/selected/disabled/error/loading/expanded等。

このPhaseでは値の良否、種類数、Token化、統合要否を判断しない。

Output: Styling Architecture Summary、Style Locations、Existing Variables / Theme Assets、Hardcoded Style Presence、Override / Dependency Findings、Similar Style Candidates、State Styling Presence、Third-party Style Boundary、Evidence。

## 04 Existing UI Assets
Inspect: Design.md / UI guideline / principles / accessibility / naming / layout、Token/theme、shared components/UI package/Storybook/pattern library、Figma等design-tool assets、icons/SVG/images/logo、coding/frontend/CSS/directory/review/lint/formatter guidelines、AGENTS.md / CLAUDE.md / Copilot / Cursor / AI Coding Guide、unit/component/E2E/visual/a11y tests、ownership/update/review/approval/deprecation。

ソースから判断できない組織情報やrepository外assetは推測しない。

Output: Existing UI Asset Inventory、Missing / Unconfirmed Assets、Development / AI Guidelines Inventory、Verification Assets、Ownership / Maintenance Questions、Evidence。

## 05 Component / Page Structure
Inspect: Page/Route inventory、parent layout、sourceから見えるpurpose、major child components、cross-page navigation、layout structure、component hierarchy、usage map、page-local/direct implementations、similar UI candidates、third-party UI boundary。

類似性のみを記録し、同一Component化の判断はしない。

Output: Page / Route Inventory、UI Structure Map、Component Classification、Component Usage Map、Page-local / Direct Implementations、Similar UI Candidates、Third-party UI Boundary、Evidence。

# Phase A Review Gate — Human Review & Context Confirmation

Phase AでAIが作成したBaseline Discoveryを人間が確認する。この工程ではUI改善・統合・System Designの判断には進まない。

## Review Classification
- **Confirmed** — Evidenceまたは既知の事実から確認できた
- **Correction Required** — 誤認、分類ミス、見逃し、Evidence不整合等を確認した
- **Human Confirmation Required** — ソースだけでは判断できずClient / Product Owner / Designer / Engineer等への確認が必要
- **Technical Verification Required** — Human Reviewer自身では技術的な正誤を十分確認できない。無理にConfirmed / Incorrectへ分類しない
- **Input Exclusion** — Audit入力から意図的に除外されたため検出不能。Blind Test、security上の除外、repository外asset等で使用し、AIの見逃しとして扱わない

## Context Confirmation Flow
AI Baseline Discovery → Human Review → Human / Technical Confirmation → Baseline Update → Baseline Confirmed → Phase B

AIがソースから調査できる事項を先に調査し、人間への質問は残ったUnknownや背景確認を中心とする。

## Phase A Completion Criteria
- Audit Scopeが確定している
- Frontend Architecture / Styling Architecture / Existing UI Assets / Component & Page StructureがBaselineとして記録されている
- Evidenceが追跡可能である
- Unknownが推測で補完されていない
- Human Review結果が記録されている
- Phase Bに影響する重大な未確認事項が解消、または明示されている

Phase Bに影響しない未確認事項は、未確認であることを明示したまま次Phaseへ進めてもよい。

# Phase B — UI Inventory
目的：既存UIに存在するFoundation、Component、Layout、Interaction / Stateを事実として棚卸しする。「種類が多い＝悪い」と評価しない。

## 06 Foundation & Token Candidate Inventory

目的：既存実装に存在するVisual Foundationを事実として抽出し、将来のToken設計・Component設計の材料となるInventoryを作成する。

このPhaseではDesign Tokenが存在することを前提にしない。既存実装にToken / Semantic naming / Scaleの概念が明示されていない場合、AIはそれらを推定・生成しない。

### 06 Audit Principles

- 既存値をそのまま記録する
- Raw valueとNormalized valueを区別する
- 値だけでなくproperty / usage context / locationを記録する
- Hardcoded / Variable / Token / Theme / Inline / SVG attribute等のdefinition typeを区別する
- 同値・近似値はCandidateとして記録できるが、統合判断はしない
- `primary-500`、`spacing-md`、`radius-sm` 等の新しいToken名を勝手に生成しない
- 既存のSemantic namingが明示されている場合のみ、その名称を記録する
- 使用目的がソースから明確でない場合、「Primary」「Error」「Surface」等の意味を推測しない
- Phase CのDivergence Analysis、Phase DのMERGE / VARIANT判断へ先回りしない

---

### 06.1 Color Inventory

#### Extract

- HEX
- Short HEX
- RGB / RGBA
- HSL / HSLA
- CSS custom properties
- Sass / theme / token values
- CSS-in-JS / style props
- Inline style
- SVG `fill` / `stroke`
- Gradient内のcolor
- `currentColor`
- Named color
- `transparent`

#### Record

- Raw Value
- Normalized Value where mechanically determinable
- Property (`color`, `background`, `border-color`, `fill`, `stroke` 等)
- Definition Type
- Variable / Token Name if explicitly present
- File / selector / component / line等のEvidence
- Usage Count when objectively measurable
- State / media-query context if present
- Foreground / background relationship when sourceから明確に確認できる場合

#### Normalization Rule

表記違いをRaw Inventoryでは保持する。

例：

- `#fff`
- `#ffffff`
- `rgb(255, 255, 255)`

機械的に同一色と判定できる場合、Normalized Valueを併記してよい。ただしRaw definition数とNormalized unique color数を混同しない。

#### Similar Color Candidates

近似色を候補として記録してよい。ただし「同じ色に統合すべき」と判断しない。

既存実装にSemantic roleが明示されていない場合、

`Primary Blueが3種類ある`

とは書かず、

`青系の近似値が3種類あり、action-like UIを含む複数箇所で使用されている`

のように、Evidenceから確認できる範囲に留める。

#### Do Not

- 新しいColor Token名を生成しない
- Contrast適否をこの工程で最終評価しない
- Brand / Primary / Secondary / Success / Warning / Error等を推測しない
- 近似色を自動統合しない

---

### 06.2 Typography Inventory

#### Extract

- `font-family`
- `font-size`
- `font-weight`
- `line-height`
- `letter-spacing`
- `font-style`
- `text-transform`
- Typography-related CSS variables / theme values
- Inline typography values
- SVG text attributes where applicable

#### Record

単一値だけでなく、可能な範囲でTypography Patternとして以下の組み合わせを記録する。

`font-family + font-size + font-weight + line-height + letter-spacing`

加えて：

- Raw declaration
- Selector / Component / Element
- Usage location
- Definition Type
- Responsive / State context
- Usage Count when objectively measurable

値が継承されており静的に確定できない場合は推測しない。

#### Do Not

- Heading / Body / Caption等のRoleを見た目だけで決めない
- Type Scaleを新規設計しない
- 近似font-sizeを統合しない

---

### 06.3 Spacing Inventory

#### Extract

- `margin`
- `margin-*`
- `padding`
- `padding-*`
- `gap`
- `row-gap`
- `column-gap`

必要に応じて、layout上の空間を作ることが明確なposition / inset等は別カテゴリとして記録し、Spacing Token候補へ自動的に混ぜない。

#### Record

- Raw declaration
- Resolved directional values where mechanically determinable
- Property
- Value
- Selector / Component
- File / line Evidence
- Definition Type
- Responsive / State context
- Usage Count when objectively measurable

例：

`padding: 8px 16px`

はRaw declarationを保持したうえで、

- vertical: `8px`
- horizontal: `16px`

として機械的に展開してよい。

#### Unit Handling

`px`、`rem`、`em`、`%`、`vw`、`vh`、`clamp()`、`calc()`等は元の単位・式を保持する。

異なる単位を、root font-size等の条件が不明なまま同一値へ換算しない。

`0` は存在として記録してよいが、Token candidate frequencyを示す場合はnon-zero valuesと区別する。

#### Do Not

- 4px / 8px Grid等を推定しない
- `15px` と `16px` を自動統合しない
- Margin / Padding / Gapのusage contextを無視して単純合算しない
- Proximityの良否を評価しない

---

### 06.4 Radius Inventory

#### Extract

- `border-radius`
- corner-specific radius
- CSS variables / theme values
- Inline values

#### Record

- Raw Value
- Normalized Value where mechanically determinable
- Target selector / component
- Usage location
- Definition Type
- Usage Count
- State / responsive context

複合値は元の宣言を保持する。

#### Do Not

- Radius Scaleを生成しない
- 近似値を統合しない
- Component roleから適正radiusを判断しない

---

### 06.5 Border Inventory

#### Extract

- `border`
- `border-*`
- border width
- border style
- border color
- outlineはBorderと混同せず、Focus等のInteraction / Stateとの関連を併記する

#### Record

可能な場合：

`width + style + color`

の組み合わせとして記録する。

加えて：

- Raw declaration
- Selector / Component
- Usage location
- State context
- Definition Type
- Usage Count

#### Do Not

- Border ruleを新規設計しない
- Focus outlineを通常Borderへ統合しない

---

### 06.6 Shadow Inventory

#### Extract

- `box-shadow`
- `text-shadow`
- `filter: drop-shadow()`
- CSS variables / theme values
- Inline values

#### Record

- Raw Value
- Shadow type
- Selector / Component
- Usage location
- Definition Type
- State context
- Usage Count

複数shadowが1 declarationに含まれる場合、Raw declarationを保持する。

#### Do Not

- Elevation Levelを勝手に命名しない
- Shadow scaleを生成しない

---

### 06.7 Size Inventory

目的：Spacingとは別に、UI要素そのものの寸法として使われている値を把握する。

#### Extract

- `width`
- `height`
- `min-width`
- `max-width`
- `min-height`
- `max-height`
- control height
- icon size
- avatar / badge等のfixed dimensions

#### Record

- Raw Value
- Property
- Target selector / component
- Fixed / min / max / fluid expression
- Usage location
- Responsive context
- Definition Type

#### Important

すべてのwidth / heightをToken候補として扱わない。

Page width、chart width、table column width、layout-specific size等と、Button height / Input height / Icon size等のrepeatable UI sizeを区別して記録する。

ただし「共通化すべき」という判断はしない。

---

### 06.8 Breakpoint Inventory

#### Extract

- CSS `@media`
- framework breakpoint config
- theme breakpoint
- JS / TSでのviewport条件
- container query if present

#### Record

- Raw condition
- Breakpoint value
- min / max / range
- File / location
- その条件下で変更されるselector / component
- 変更内容の概要

同じ数値でも用途が異なる場合はusage contextを保持する。

#### Do Not

- Breakpoint Scaleを生成しない
- 近似Breakpointを統合しない
- Device categoryをEvidenceなしに断定しない

---

### 06.9 Icon / Visual Representation Inventory

IconはColor / Spacing等のToken Candidateとは完全に同列に扱わず、Visual Asset / Component Inventoryとして記録する。

#### Extract

- Shared Icon Component
- Third-party Icon Library
- Inline SVG
- SVG asset
- CSS-generated icon
- Unicode glyph / symbol
- Image-based icon
- Icon-like pseudo-element

#### Record

- Representation Method
- Icon name if explicitly available
- Source / component
- Size
- Color mechanism (`currentColor`, hardcoded fill等)
- Usage location
- State context
- Accessible name / `aria-*` 等がsourceから確認できる場合

#### Do Not

- Icon library統一を決定しない
- 見た目が似ているだけで同一iconと断定しない

---

## 06 Similar / Related Value Candidates

各Foundationについて、以下をCandidateとして記録できる。

- Mechanically identical values with different notation
- Near values
- Repeated values
- Same value used in different properties
- Similar visual roles visible from source

ただしCandidateの存在は、統合・Token化・Semantic role付与の決定ではない。

---

## 06 Accessibility Preparation

Phase BではAccessibilityの最終評価を行わないが、Phase Cで評価可能にするため、sourceから明確に確認できる以下の関係をEvidenceとして保持してよい。

- foreground / background color pair
- focus-visible styling
- disabled styling
- error / destructive styling
- text / icon color inheritance
- control size
- icon accessible name / aria attributes

Contrast ratio、WCAG適合性、Focus visibilityの妥当性等の評価はPhase Cへ送る。

---

## 06 Output Format

成果物は最低限、以下の順で構成する。

### A. Foundation Summary

各カテゴリについて：

- Raw Definitions
- Unique / Normalized Values where mechanically determinable
- Definition Types
- Main Locations
- Unresolved / Human Confirmation Required

### B. Raw Inventory

カテゴリ別に、可能な限り以下を表形式で記録する。

- Raw Value
- Normalized Value
- Property / Pattern
- Definition Type
- Usage Count
- File / Selector / Component
- State / Responsive Context
- Evidence

### C. Similar / Related Value Candidates

同値表記違い、近似値、繰り返し値等を候補として記録する。

### D. Existing Token / Variable Inventory

既存Token / CSS Variable / Theme等が存在する場合のみ記録する。

- Name
- Value
- Scope
- Usage
- Evidence

存在しない場合は「Not found in supplied source」とする。新規Tokenを生成しない。

### E. Human Confirmation Required

sourceから判断できない意味、repository外Token、Design Tool側Variable、legacy / deprecated status等。

### F. Phase Boundary

以下を明記する。

- Token設計は実施していない
- Semantic namingは新規付与していない
- 値の統合判断はしていない
- UI品質評価はしていない
- Phase 07以降へ進んでいない

---

## 06 Recommended Deliverable

Phase 06単独で実行する場合の標準成果物名：

`foundation-inventory.md`

必要に応じて機械集計用のCSV / JSON等を補助成果物として作成してよい。ただしHuman-readableな`foundation-inventory.md`を主成果物とする。

## 07 Component & UI Part Inventory

目的：既存UIに存在する部品を、Componentとして整理・統合する前に事実として発見し、実装形態、構造、見た目、用途、State、使用箇所の差異を追跡可能にする。

このPhaseで扱う「UI Part」は、shared componentとして実装されたものだけを意味しない。画面上で一つの操作・入力・表示・移動・通知等を担うまとまりを、実装方式にかかわらずInventory対象とする。

例：

- Shared Componentとして実装されたButton
- Domain Component内に閉じたSelect
- Page-local CSSで作られたCard-like UI
- JSX / HTMLへ直接記述されたIcon Button-like control
- Third-party libraryを直接使用したModal
- 同じ見た目だが異なる実装を持つTabs-like UI

このPhaseでは、それらを同一Componentへ統合すべきか、Variantにすべきか、廃止すべきかを決定しない。

### 07 Audit Principles

- Component名が存在する場合は既存名を記録する
- Component名がない場合は、観測上の呼称として `*-like UI` または `Observed UI Part` を使用してよい
- file名、directory名、class名だけを根拠に用途や再利用意図を確定しない
- 見た目が似ているもの、用途が似ているもの、構造が似ているものを区別する
- Shared / Domain / Page-local / Direct markup / Third-party wrapper / Third-party direct useを区別する
- Wrapperと内部で利用されるThird-party Componentを分けて記録する
- Props、class、DOM、style、usage locationから確認できる差分を記録する
- Runtimeでしか確認できない表示・挙動は推測せず `Runtime Verification Required` とする
- 類似UIは `Similar Component Candidate` として記録できるが、統合・Variant化を決定しない
- Componentの存在数、実装数、見た目の種類数を混同しない
- Phase 08のLayout、Phase 09のInteraction / State、Phase CのPattern / Divergence、Phase DのHuman Decisionへ先回りしない

---

### 07.1 Inventory Scope

最低限、以下のComponent family / UI Part familyを調査する。製品に存在しないfamilyを無理に生成しない。

#### Action

- Button
- Icon Button
- Link / Link-like Action
- Split Button
- Menu Trigger
- Floating / Sticky Action

#### Form / Input

- Text Input
- Textarea
- Search
- Select
- Combobox / Autocomplete
- Checkbox
- Radio
- Switch / Toggle
- Date / Time Input
- File Upload
- Form Field / Label / Helper / Error Message

#### Navigation

- Global / Local Navigation
- Sidebar
- Header Navigation
- Breadcrumb
- Tabs
- Segmented Control
- Pagination
- Stepper
- Menu / Dropdown Menu

#### Data Display

- Table / Data Grid
- List
- KPI / Metric
- Card
- Badge / Status
- Tag / Chip
- Avatar
- Progress
- Chart wrapper / Legend / Data label
- Key-value / Description List

#### Container / Disclosure

- Panel
- Section
- Accordion
- Disclosure
- Collapsible Area
- Drawer / Side Panel

#### Feedback / Status

- Alert
- Toast / Snackbar
- Inline Message
- Loading Indicator / Skeleton
- Empty State
- Error State
- Success / Completion State

#### Overlay / Contextual UI

- Modal / Dialog
- Popover
- Tooltip
- Context Menu
- Confirmation UI

#### Visual / Utility

- Icon wrapper
- Divider
- Spacer-like element
- Image / Thumbnail wrapper
- Scroll container
- Sticky / Fixed utility UI

---

### 07.2 Discovery Method

単一の探索方法だけでComponent一覧を確定しない。可能な範囲で以下を組み合わせる。

1. **Definition Discovery**
   - component directories
   - exported components
   - shared UI packages
   - Storybook stories
   - theme / component override
   - third-party UI imports

2. **Usage Discovery**
   - imports / references
   - JSX / template usage
   - route / page usage
   - conditional rendering
   - repeated direct markup

3. **Style Discovery**
   - component-specific styles
   - shared classes
   - page-local classes
   - variant / modifier classes
   - inline styles / style props

4. **Structure Discovery**
   - DOM / JSX / template structure
   - child elements
   - slot / composition structure
   - labels, icons, helper text, headers, footers等

5. **State Surface Discovery**
   - props / attributes / classes / conditional branchesとして存在するState
   - actual interaction qualityの評価はPhase 09へ送る

Definitionが存在しても使用箇所が見つからない場合は `Defined, Usage Not Found` とする。使用箇所はあるがdefinitionを一意に追跡できない場合は `Used, Definition Unresolved` とする。

---

### 07.3 Record per UI Part

各UI Partについて、確認可能な範囲で以下を記録する。

#### Identity

- Existing Component Name
- Observed UI Part Name if no explicit name exists
- Component Family
- Source Type
  - Shared
  - Domain
  - Page-local
  - Direct Markup
  - Third-party Wrapper
  - Third-party Direct Use
  - Unknown
- Definition Location
- Export / import path where applicable

#### Usage

- Usage Locations
- Route / Page / Parent Component
- Usage Count when objectively measurable
- Repeated / one-off usage
- Conditional usage where sourceから確認できる場合

`Usage Count` は静的参照数、render箇所数、画面数等の数え方を明示する。実利用頻度やユーザー操作頻度として扱わない。

#### Structure

- Root element / underlying element
- Major child parts
- Composition / slots
- Label / icon / helper / header / footer等の有無
- Relevant DOM / JSX / template pattern

#### Inputs / API

- Props / attributes / parameters
- Enumerated option values
- Boolean flags
- Children / slots
- Event handlers
- Defaults where explicitly defined

Propsの存在を、意図された正式Variantであると自動的に解釈しない。

#### Visual Characteristics

Sourceから確認できる範囲で以下を記録する。

- size / dimensions
- spacing
- color / border / radius / shadow
- typography
- icon presence / position
- density
- alignment
- relevant class / style source

Foundation値はPhase 06のInventoryと相互参照してよい。ここで新しいTokenやStyle ruleを作成しない。

#### State Surface

- Default
- Hover
- Focus / Focus Visible
- Active / Pressed
- Selected / Current
- Disabled
- Read-only
- Error / Invalid
- Loading
- Empty
- Expanded / Collapsed
- Open / Closed
- Checked / Indeterminate

この項目では、Stateの存在・実装場所・表現差分を記録する。Keyboard interaction、feedback adequacy、accessibility妥当性等の評価はPhase 09またはPhase Cへ送る。

#### Dependency / Ownership Boundary

- Custom implementation
- Third-party package / version where known
- Wrapperの有無
- local overrideの有無
- style dependency
- parent context dependency

組織上のOwnerはコードから確認できない限り推測しない。

#### Evidence

- File path
- Component / function / class / selector
- Relevant line or source location where available
- Import / usage reference
- Story / test / screenshot reference if supplied

---

### 07.4 Existing Variant-like Differences

以下の差異を `Observed Difference` として記録できる。

- prop valueによる差分
- modifier classによる差分
- size差分
- emphasis / visual treatment差分
- icon-only / icon-leading / text-only等の構造差分
- domain / pageごとの差分
- shared componentとdirect markupの差分
- wrapper有無の差分
- third-party設定値の差分

既存コードに `variant` という名前があっても、その分類がUX / Design上妥当とは限らない。反対に、`variant` という名前がなくても複数の実装差分が存在し得る。

したがってこのPhaseでは、以下を分けて記録する。

- **Explicit Variant** — source上でvariant / type / size等として明示されている
- **Implicit Variation** — class、props、structure、style等の組み合わせにより差分が生じている
- **Separate Implementation** — 別Componentまたはdirect markupとして存在する
- **Unknown** — sourceだけでは関係を確定できない

### 07.5 Similar Component Candidates

類似候補は、少なくともどの観点で似ているかを記録する。

- Similar Purpose Candidate
- Similar Structure Candidate
- Similar Visual Candidate
- Similar Interaction Candidate
- Similar API Candidate
- Duplicate-looking Direct Implementation Candidate

一つの観点だけが似ている場合、「同一Component」と表現しない。

例：

- 見た目は似ているが用途が異なる
- 用途は似ているがDOM / API / dependencyが異なる
- 同じshared componentを使用しているがpage-local overrideで見た目が異なる
- 別実装だが構造とstyleが近い

候補ごとにEvidenceと未確認事項を残す。

---

### 07.6 Family-specific Inspection

#### Button / Action

- underlying element (`button`, `a`, clickable `div`等)
- label / icon structure
- type / role
- explicit / implicit variations
- size
- disabled / loading surface
- usage location

このPhaseではPrimary / Secondary / Destructive等の意味を、既存名または使用文脈から明確に確認できない限り付与しない。

#### Form Controls

- label associationがsourceから確認できるか
- helper / error message structure
- required / optional indication
- disabled / read-only / invalid surface
- native / custom / third-party
- field wrapperの共通性

妥当性の最終評価はPhase 09 / Phase Cへ送る。

#### Card / Panel / Container

- container structure
- header / body / footer
- clickable / non-clickable
- border / radius / shadow / spacing
- layout dependency

見た目がCard-likeでも、単なるlayout containerの可能性を残す。

#### Accordion / Disclosure

- trigger / content structure
- open state source
- single / multiple behaviorがsourceから確認できるか
- shared / direct implementation
- icon / indicator

Interactionの正しさやkeyboard behaviorはPhase 09で扱う。

#### Modal / Dialog / Overlay

- trigger relation
- wrapper / portal / third-party dependency
- header / body / footer / close control
- size / placement option
- confirmation / information / form等、sourceから確認できるusage context

Focus trap、escape、background interaction等はPhase 09で扱う。

#### Table / Data Grid

- native table / custom grid / third-party data grid
- header / body / footer structure
- toolbar / filter / pagination relation
- column definition method
- sorting / selection / expansion等のsurface
- density / sticky / scroll / responsive configuration
- cell-local direct UI

同じTable Componentでもcolumn / cell renderer / page-local CSSにより大きな差異が生じるため、Component definitionだけでなくusage configurationも確認する。

#### Navigation / Tabs / Pagination

- underlying element / routing mechanism
- current / selected surface
- shared / page-local structure
- icon / label
- overflow / responsive condition if sourceから確認できる場合

#### Badge / Status / Tag

- displayed value / label source
- visual variation mechanism
- interactive / non-interactive
- icon / dot / text structure
- surrounding context

色だけを根拠にSemantic roleを断定しない。

#### Tooltip / Toast / Alert

- trigger / display source
- third-party / custom
- placement / duration / dismissal option where explicit
- live region / role等がsourceから確認できる場合

実際の通知タイミングや認知性は、このPhaseでは評価しない。

---

### 07.7 Accessibility Preparation

Phase 07ではAccessibilityの最終評価を行わないが、後続Phaseで検証可能にするため、sourceから明確に確認できる以下を記録してよい。

- underlying semantic element
- `role`
- accessible name source
- `aria-*`
- `disabled` / `aria-disabled`
- `aria-selected` / `aria-current`
- `aria-expanded` / `aria-controls`
- label association
- keyboard handler presence
- focus target / `tabindex`
- icon-only controlのlabel有無

属性が存在することと、正しく機能していることを同一視しない。適合性、操作性、読み上げ結果等の評価はPhase 09 / Phase Cへ送る。

---

## 07 Output Format

成果物は最低限、以下の順で構成する。

### A. Component Inventory Summary

Component familyごとに：

- Observed UI Parts
- Existing Definitions
- Source Type distribution
- Main Usage Locations
- Third-party Dependencies
- Unresolved / Human Confirmation Required

数を示す場合、以下を区別する。

- Component definitions
- UI Part implementations
- Explicit variants
- Usage references / locations
- Similar Component Candidates

### B. Component / UI Part Inventory

可能な限り、以下を表形式で記録する。

- Inventory ID
- Existing / Observed Name
- Family
- Source Type
- Definition Location
- Usage Locations / Count basis
- Structure
- Props / Inputs
- Explicit Variants
- Implicit Variations
- State Surface
- Third-party Dependency
- Evidence

### C. Component Usage Map

- UI Part / Component
- Route / Page
- Parent / Context
- Usage form
- Page-local override / configuration
- Evidence

### D. Similar Component Candidates

- Candidate Group ID
- Candidate Items
- Similarity Type
- Confirmed Differences
- Unknowns
- Evidence

統合提案や推奨Component APIは記載しない。

### E. Direct / Local / Third-party Implementations

- Direct markup
- Page-local UI
- Domain-only UI
- Third-party direct use
- Third-party wrapper
- local override

を追跡可能にする。

### F. Runtime / Human Confirmation Required

- Runtimeでしか確認できない表示・挙動
- sourceから意味を確定できないvariation
- repository外component / Storybook / design asset
- legacy / deprecated status
- intentional differenceか偶発的差異か
- usage frequency / business importance

### G. Phase Boundary

以下を明記する。

- Component統合は実施していない
- Variant設計は実施していない
- Component API再設計は実施していない
- Shared化 / refactoringは実施していない
- UI品質評価は実施していない
- Accessibility適合性は最終評価していない
- KEEP / MERGE / VARIANT / REMOVE / REVIEW判断は実施していない
- Phase 08以降へ進んでいない

---

## 07 Recommended Deliverable

Phase 07単独で実行する場合の標準成果物名：

`component-inventory.md`

必要に応じて機械集計用のCSV / JSON、definition-to-usage map等を補助成果物として作成してよい。ただしHuman-readableな`component-inventory.md`を主成果物とする。

## 08 Layout Inventory

目的：既存UIに存在する画面構造、領域分割、配置、整列、余白、幅、高さ、Grid、Responsive、Scroll等を事実として抽出し、将来のLayout rule・Page template・Component composition設計の材料となるInventoryを作成する。

このPhaseでは理想的なLayoutを新規設計しない。複数画面に似た構造が存在しても、共通Template化、Container化、Grid統一、Spacing統一を決定しない。

LayoutはComponentと重なる場合がある。Page Heading、Toolbar、Card Grid、Table Panel、Modal Header等がComponentとして実装されている場合でも、このPhaseでは「画面や領域の中で何をどのように配置しているか」という構造・配置の観点から記録する。

### 08 Audit Principles

- 現在のLayout構造と値をそのまま記録する
- Page / Route / Layout / Component / CSSの関係を追跡可能にする
- 見た目上の領域名と既存Component名を区別する
- 既存名がない領域には `*-like Layout` または `Observed Layout Region` 等の観測上の呼称を使用してよい
- DOM / JSX階層とVisual Layout hierarchyを同一視しない
- Flex / Grid / block / table / position等のlayout mechanismを区別する
- width / height / padding / margin / gap等はPhase 06と相互参照してよい
- 同じ値でもPage spacing、Component internal spacing、Table cell spacing等のusage contextを保持する
- Responsive条件下の変更をbase layoutと分けて記録する
- Hidden / reordered / wrapped / stacked / scrollable等の変化を記録する
- Static sourceから確定できないcomputed size、overflow結果、実際の折返し等は `Runtime Verification Required` とする
- 類似Layoutは `Similar Layout Candidate` として記録できるが、共通Template化を決定しない
- Layout差異の存在と、その差異が意図的かどうかを分ける
- Proximity、Hierarchy、Cognitive Load等の良否評価はPhase Cへ送る
- Phase 09のInteraction / State、Phase CのAnalysis、Phase DのHuman Decisionへ先回りしない

---

### 08.1 Inventory Scope

最低限、以下のLayout familyを調査する。製品に存在しないfamilyを無理に生成しない。

#### Application Shell

- Root layout
- Sidebar / Navigation rail
- Top bar / Header
- Main content region
- Footer
- Utility / account region
- Fixed / sticky / static regions
- Shell-wide responsive behavior

#### Page

- Page root
- Content width / max-width
- Horizontal / vertical page padding
- Page background
- Page top / bottom spacing
- Full-width / contained / split layout

#### Page Heading

- Eyebrow / breadcrumb
- Title
- Description
- Metadata
- Primary / secondary action area
- Heading alignment and wrapping
- Heading-to-content spacing

#### Section / Content Group

- Section boundary
- Section heading / body
- Section spacing
- Nested section
- Divider / background / borderによる区切り

#### Grid / Repeated Content

- CSS Grid
- Flex row / wrap
- Repeated card arrangement
- Column count / track definition
- Minimum / maximum size
- Gap
- Responsive column change

#### Toolbar / Filter / Action Area

- Search / Filter / Action area
- Summary / result count
- Alignment / distribution
- Wrapping / stacking / reordering
- Toolbar-to-content relationship

#### Form Layout

- Vertical field stack / horizontal field row
- Label / control / helper placement
- Multi-column form
- Required indicator placement
- Action row
- Responsive field arrangement

#### Table / Data Region

- Toolbar / summary / table / pagination / empty-state composition
- Table container
- Horizontal / vertical scroll boundary
- Sticky header / column where sourceから確認できる場合
- Column width definition
- Cell alignment / padding
- Footer / caption / status area
- Responsive hiding / stacking

#### Detail / Master–Detail / Disclosure

- Parent list / trigger / detail relationship
- Inline expansion / side-by-side detail
- Nested content
- Open content placement
- Summary / detail boundary

#### Overlay

- Dialog / Modal dimensions
- Header / body / footer composition
- Internal spacing
- Action placement
- Viewport constraints
- Backdrop / portal / stacking context where sourceから確認できる場合

#### Scroll / Overflow / Sticky

- `overflow-*`
- scroll container
- fixed / sticky positioning
- viewport-relative height
- table / navigation overflow
- hidden content
- scroll ownership

#### Responsive / Adaptive Layout

- media / container query
- JS / TS viewport condition
- breakpointごとのcolumn change
- hide / show / reorder / wrap / stack
- padding / gap / size change
- navigation transformation

---

### 08.2 Discovery Method

単一のCSS propertyだけでLayoutを確定しない。可能な範囲で以下を組み合わせる。

1. **Route / Page Discovery** — root / nested layout、page entry、route composition
2. **Structure Discovery** — DOM / JSX hierarchy、parent / child、repeated / conditional regions
3. **Layout Mechanism Discovery** — display、flex、grid、position、size constraint、spacing、overflow
4. **Responsive Discovery** — media / container query、breakpoint utility、conditional rendering、responsive props
5. **Composition Discovery** — Components、slots / children、Page-local wrappers、direct markup、shared selectors
6. **Usage Discovery** — route / page、parent region、data-dependent repetition、context variation

CSS declarationが存在しても対象markupから参照されていない場合は、実在Layoutとして数えない。参照未検出のstyleは `Defined, Usage Not Found` として別記する。

---

### 08.3 Record per Layout Pattern

各Layout Pattern / Regionについて、確認可能な範囲で以下を記録する。

#### Identity

- Existing Name / Observed Layout Name
- Layout Family / Inventory ID
- Route / Page / Parent Component
- Definition / Usage Location

#### Structure

- Parent region
- Major child regions
- Order / Nesting
- Repeated / conditional regions
- Semantic element where explicit

#### Layout Mechanism

- display mode
- flex direction / wrap / alignment / distribution
- grid tracks / areas / auto flow
- position
- width / height constraints
- margin / padding / gap
- overflow / scroll
- relevant selector / class / style source

#### Responsive Behavior

- Base layout
- Breakpoint / condition
- Changed region
- Before / after structure or property
- Hidden / shown / reordered / wrapped / stacked

#### Composition Boundary

- Shared Layout Component
- Shared UI Component composition
- Domain composition
- Page-local wrapper
- Direct markup
- Third-party layout utility / component

#### Evidence

- File path
- Route / Page
- Component / function
- Selector / class
- Relevant line or source location where available
- Related Foundation / Component Inventory ID where available

---

### 08.4 Layout Level Classification

- **L1 — Application Shell:** 全体NavigationとMain region
- **L2 — Page:** Page rootと主要領域
- **L3 — Section:** Page内の意味的・視覚的区分
- **L4 — Pattern Composition:** Toolbar、Card Grid、Table Region、Form等
- **L5 — Component Internal Layout:** Component内部の配置

同じCSS Grid / FlexであってもLevelが異なる場合は単純に同一Layoutとしてまとめない。L5は他Levelとの関係を理解するために必要な範囲で記録し、Component内部のVariant設計には進まない。

---

### 08.5 Similar Layout Candidates

以下をCandidateとして記録できる。

- Similar Page Structure / Page Heading / Section Composition
- Similar Card Grid / Toolbar / Filter Layout / Form Layout
- Similar Table Region / Detail / Disclosure Layout / Modal Composition
- Duplicate-looking Direct Layout
- Same class with different child composition
- Different classes with similar visual structure

候補ごとにSimilarity Type、Confirmed Common Structure、Confirmed Differences、Responsive Differences、Usage Context、Unknowns、Evidenceを分けて記録する。

Candidateの存在は、共通Template、Shared Layout Component、同一Grid、同一Spacing ruleへ統合すべきという決定ではない。

---

### 08.6 Responsive / Runtime Verification Preparation

Static sourceから確認できる宣言と、Runtimeでの実表示を分ける。

#### Static Sourceで記録できるもの

- breakpoint condition / property change / Component branch
- hidden / shown declaration
- grid track / flex direction / wrap change
- width / padding / gap change
- overflow declaration

#### Runtime Verification Required

- actual computed width / height
- text wrapping / content overflow / scrollbar発生
- sticky / fixedの実挙動
- viewport内の重なり
- dynamic contentによる高さ変化
- table columnの実寸
- dialogのviewport fit
- zoom / font scaling時のLayout

Runtime確認が必要な項目を、Static sourceの不足や不具合として自動判定しない。

---

### 08.7 Accessibility Preparation

Phase 08ではAccessibilityの最終評価を行わないが、後続Phaseで検証可能にするため、sourceから明確に確認できる以下を記録してよい。

- landmark element / role
- heading hierarchyとLayout regionの関係
- DOM orderとVisual order
- responsive時の非表示 / reordering
- scroll container / sticky / fixed region
- zoom / reflowへ影響するfixed size
- target sizeに関係するLayout dimension

DOM order、reading order、reflow、zoom、target size等の適合性評価はPhase Cへ送る。

---

## 08 Output Format

### A. Layout Inventory Summary

Layout family / Levelごとに、Observed Patterns、Routes / Pages、Mechanisms、Responsive conditions、Composition Boundary、Unresolved itemsを記録する。

### B. Route / Page Layout Map

- Route / Page
- L1 Application Shell
- L2 Page regions
- L3 Sections
- L4 Pattern compositions
- Major child Components / UI Parts
- Evidence

### C. Layout Pattern Inventory

- Layout Inventory ID
- Existing / Observed Name
- Level / Family
- Route / Page / Parent
- Structure / Layout Mechanism
- Size / Spacing / Constraint
- Responsive Behavior
- Composition Boundary
- Related Component Inventory ID
- Evidence

### D. Responsive Behavior Matrix

- Breakpoint / Condition
- Affected Route / Region
- Base / Changed behavior
- Hide / Show / Reorder / Wrap / Stack / Resize / Scroll
- Evidence
- Runtime Verification Required

### E. Similar Layout Candidates

- Candidate Group ID / Candidate Items
- Similarity Type
- Confirmed Common Structure / Differences
- Responsive Differences
- Unknowns / Evidence

共通Template案や推奨Layout ruleは記載しない。

### F. Scroll / Overflow / Sticky Inventory

- Region / Owner / Parent
- Mechanism
- Width / height constraint
- Responsive condition
- Runtime verification item
- Evidence

### G. Runtime / Human Confirmation Required

computed layout、responsive実表示、dynamic content、browser / device constraints、repository外Layout rule、intentional difference、business importance等。

### H. Phase Boundary

- Layout統合は実施していない
- Page Template設計は実施していない
- Grid / Container / Spacing ruleの新規設計は実施していない
- Shared Layout Component化は実施していない
- Responsive strategyの再設計は実施していない
- Proximity / Hierarchy等のUI品質評価は実施していない
- Accessibility適合性は最終評価していない
- KEEP / MERGE / VARIANT / REMOVE / REVIEW判断は実施していない
- Phase 09以降へ進んでいない

---

## 08 Recommended Deliverable

Phase 08単独で実行する場合の標準成果物名：

`layout-inventory.md`

必要に応じてRoute / Page構造図、Responsive Matrix、Figma / FigJam上のVisual Layout Map、CSV / JSON等を補助成果物として作成してよい。ただしHuman-readableな`layout-inventory.md`を主成果物とする。

Visual Layout Mapを作成する場合は、既存Layoutの現状再現に限定し、改善案・共通Template案・推奨Gridとして扱わない。各LayoutにはInventory IDを付与し、`layout-inventory.md`と相互参照できる状態にする。

## 09 Interaction / State
Hover、Focus / Focus Visible、Active、Selected、Disabled、Error、Loading、Empty、Expanded / Collapsed、Click / Tap area、Keyboard interaction、Feedbackを確認する。

# Phase C — Analysis

## 10 Pattern
Repeated usage、common layout/component behavior、shared visual pattern、domain-specific patternを整理する。

## 11 Divergence
同じ／似た役割に対する差異を記録する。同じPrimary Actionの複数style、Accordionの複数interaction、Table density、Selected state、近似spacing/color/radius、Shared ComponentとDirect Markupの併存等。差異の存在と差異の意味を分ける。

## 12 UX / Design Review
Proximity、Similarity、Alignment、Hierarchy、Consistency、Visibility / Affordance、Feedback、Cognitive Load、Information Density、Data-heavy UI usability、Accessibilityを確認する。

Spacing Audit（どの値が存在・分岐しているか）とProximity Audit（visual spacingがsemantic groupingを正しく表現しているか）を混同しない。

# Phase D — Human Decision

## 13 Human Decision
AIのInventory / AnalysisをEvidenceとして、人間が最終判断する。

Decision Label: **KEEP / MERGE / VARIANT / REMOVE / REVIEW**

Business context、User task、Frequency、Risk、Accessibility、Engineering constraints、Existing usage、Migration cost、Legacy / future roadmapを考慮する。AIはDecisionを自動確定しない。

# Phase E — Systemize

## 14 System Design
Human Decisionを基にUI開発基盤へ落とし込む。

### Design.md
UI rules、Decision criteria、Layout rules、Component usage、State rules、Accessibility rules、Naming、Do / Don't。

### UI Components
Shared components、Variants、States、Component API、Source code。

### Storybook
Component catalog、Variants、States、Usage examples、Accessibility / interaction references。

### AI Coding Guide
Design.mdを参照する。Storybook / existing componentsを先に探索する。Existing Componentを優先して再利用する。Existing Variantで表現できる場合は新規Component / CSSを作らない。新規UI Patternを勝手に追加しない。新規Componentが必要な場合の判断・確認ルール、Token / naming / accessibility ruleに従う。

# Phase F — Verification

## 15 Verification
Systemize後、既存UIが整ったかだけでなく、**今後UIを作り続けられる基盤として機能するか**を検証する。

### Re-Audit
Foundation divergence、Component divergence、Layout divergence、Interaction divergence、Direct implementation、Legacy implementation、Accessibility findingsをBefore Auditと比較する。

### AI Agent Implementation Test
AI Agentに、既存画面の単純コピーではない新規UI要件を与えて実装させる。通常の開発環境と同様にDesign.md、Storybook、Existing UI Components、AI Coding Guide、Relevant source codeを参照可能にする。

### Verify — Component Discovery
AI AgentがStorybook / sourceから既存Componentを発見できるか。

### Verify — Component Selection
AI Agentが要件に対して適切な既存Componentを選択できるか。例：Primary action→existing Button、Destructive action→destructive Variant、Data listing→Table、Confirmation→Modal/Dialog、Search→Search Component。

### Verify — Variant Selection
新規Componentを作る前に、既存Variant / Stateで要件を満たせるか判断できるか。

### Verify — Rule Compliance
Design.md、Existing Component、Existing Token、AI Coding Guide、Accessibility ruleに従い、不要な独自CSS / Component / Patternを増やしていないか確認する。

### Failure Signals
Existing Componentがあるのに新規作成、Existing Variantがあるのに新規style、Storybook componentを発見できない、用途に合わないVariant、Design.mdと異なるPattern、不要な独自class/hardcoded value、Accessibility requirementの欠落。

Failure時はStorybook documentation、Component naming/API/architecture、Design.md、AI Coding Guide、Token structure、Prompt / agent context、Human Decisionのどこへ戻すべきか分析する。

### Verification Goal
最終ゴールは「After画面が綺麗になった」ことではない。

**未知の新規UI要件に対しても、AI AgentがStorybookと既存UI基盤から適切なComponent / Variant / Ruleを発見・選択・再利用し、不要なUI分岐を増やさず実装できる状態**を確認する。

# Comparative Evaluation — Mode B Only

Mode BではPhase A〜CをBefore / Afterへ同じ条件で適用する。必要に応じてPhase Fの検証観点も使用するが、Phase DのHuman DecisionおよびPhase EのSystemizeは比較監査の必須工程ではない。

## Execution Order

1. Comparison Audit Contractを固定する
2. BeforeをPhase A〜Cの対象範囲で監査する
3. Afterを同じPhase、criteria、粒度、Evidence requirementで監査する
4. Stable Keyと観察対象を用いてFindingを対応付ける
5. Comparison Statusを判定する
6. 視覚変化と実装構造変化を分離して記録する
7. Comparison SummaryとComparison Matrixを作成する
8. Human / Technical / Runtime confirmationが必要な項目を分離する

比較のバイアスを抑えるため、可能な場合はBefore / AfterそれぞれのInventoryとFindingを先に確定し、差分判定はその後に行う。盲検化が可能な運用では入力名を中立化してよいが、監査対象の同一性やEvidence追跡性を失わせない。

## Comparison Status

| Status | Definition | Minimum Evidence |
| --- | --- | --- |
| **Resolved** | Beforeで確認された問題・分岐・欠落がAfterでは確認されず、対象が削除されたためではなく解消したことを確認できる | Beforeの存在Evidence、Afterの再調査Evidence、解消形態 |
| **Improved** | 問題は残るが、同一指標・同一観点で状態が良い方向へ変化した | 双方のEvidenceと比較可能な差。残存課題を明記 |
| **Unchanged** | 同一Findingまたは同等状態がAfterにも存在し、意味のある差を確認できない | 双方のEvidence。差がないことを省略しない |
| **Regressed** | Beforeより悪化した、またはAfterの変更により同一領域の品質・一貫性・構造上の問題が増大した | 双方のEvidenceと悪化内容 |
| **Newly Detected** | Afterで初めて存在するFinding。Beforeの見逃しではなく、Beforeに存在しなかったことを確認できる | Afterの存在EvidenceとBeforeの再確認Evidence |
| **Not Comparable** | 条件、要件、範囲、Evidence、実行環境、対象構造等が揃わず方向判定できない | 比較不能理由と影響範囲 |

`Resolved`と`Improved`は分ける。対象UIの削除、route除外、機能未実装により見えなくなっただけの場合はResolvedとせず、`Not Comparable`または別のscope changeとして扱う。

`Newly Detected`は「今回初めて監査者が気づいた」ではなく、「Afterで新たに存在する」とEvidenceで確認できる場合に使う。Beforeの再調査で既存だった場合は対応するBefore Findingを追加し、適切なStatusへ修正する。

Statusはseverityではない。重大度・影響度を扱う既存または別の分類がある場合、独立したfieldとして保持する。

## Comparison Dimensions

各Dimensionは存在する範囲で評価し、製品に存在しない項目を無理に生成しない。

### Foundation / Styling

- Design Token / CSS variable / theme valueの使用実態
- Color、Typography、Spacing、Radius、Border、Shadow、Size、Breakpointの定義数・分岐・usage context
- hardcoded values、inline style、SVG attributes、page-local overrides
- selector specificity、override chain、style dependency

Token名や意味の妥当性ではなく、実装上の参照・再利用・分岐をEvidenceとして扱う。Design.md Ruleへの準拠判定は行わない。

### Component / UI Part

- shared / domain / page-local / direct markup / third-partyの構成
- component reuse、definition-to-usage relation
- explicit variant、implicit variation、separate implementation
- duplicate-looking implementation、wrapper / override、API / structure

見た目が似ていても別実装なら、その構造差を記録する。shared component化されても不適切なoverrideや過剰分岐が増えた場合は改善と決めつけない。

### Layout / Responsive

- layout hierarchy、composition boundary、grid / flex / size constraint
- repeated layout、page-local wrapper、scroll / overflow / sticky
- breakpoint、hide / show / reorder / wrap / stack
- source宣言とruntime結果

### Interaction / State / Accessibility

- Hover、Focus / Focus Visible、Active、Selected、Disabled、Error、Loading、Empty、Expanded / Collapsed
- keyboard interaction、feedback、semantic element、accessible name、ARIA relation、label association
- target size、reading / DOM order、reflow等

属性の追加だけで改善とせず、実際の関係・挙動が確認できる範囲で評価する。runtimeや支援技術確認が必要なら明示する。

### UI Consistency / UX Structure

- 同じ／似た役割に対する視覚・構造・interactionの差異
- hierarchy、proximity、alignment、affordance、feedback、information density
- data-heavy UIにおけるscanability、操作位置、状態識別

定量化できない項目を擬似的な数値へ変換しない。観察条件とEvidenceを示す。

## Visual and Structural Change Matrix

視覚変化と内部構造変化を別軸で記録する。

| Visual Change | Structural Change | Interpretation Rule |
| --- | --- | --- |
| Improved | Improved | 両面の改善Evidenceを記録する |
| Improved | Unchanged | 見た目の改善のみ。構造改善とは表現しない |
| Improved | Regressed | 表面的改善と内部悪化を併記し、総合的にResolvedとしない |
| Unchanged | Improved | 見た目が同じでもreuse / token / component構造の改善を記録する |
| Unchanged | Unchanged | `Unchanged`として残す |
| Regressed | Improved | 構造改善と視覚・UX悪化を分離する |
| Not Comparable | Any | 比較不能理由を優先して明記する |

`Visual`と`Structural`の片方しか確認できない場合、未確認側を推測しない。

## Quantitative Evidence Rules

比較値を示す場合は、同じ抽出方法・母数・単位を使う。

例：

- unique normalized color values
- hardcoded declaration count
- shared component usage references
- direct markup implementations
- explicit / implicit variant count
- duplicate implementation candidate count
- routes / screens with focus-visible source
- responsive conditions / breakpoints

countの減少自体を自動的に改善としない。用途差、対象削除、scope差、集計方法差を確認する。割合を示す場合は分子・分母と除外条件を記録する。

## Causal Attribution

比較監査は変化を観察するものであり、Design.mdが変化の唯一の原因であることを自動的に証明しない。

- 同一要件、同一入力、同一環境でDesign.md参照有無のみが主要差である場合、`Design.md導入と関連する変化`と記述できる
- model、prompt、dependency、data、runtime、manual edit等も異なる場合は交絡要因として記録する
- Design.mdを使ったという事実だけで因果関係を確定しない
- Compliance Verificationの結果とUI Auditの変化が対応する場合も、相関と因果を区別する

# Comparative Deliverables — Mode B Only

## 1. Comparison Summary

最低限、以下を含める。

- Audit Mode / Audit Scope
- Before / After source snapshots and runtime conditions
- Requirements / Target Screens / Flows
- Comparison Audit Contractの一致状況
- Comparison Constraints / Exclusions
- Comparable Findings
- Improved / Resolved / Unchanged / Regressed / Newly Detected / Not Comparable件数
- Human Confirmation Required件数
- 改善した領域
- 変化しなかった領域
- 悪化した領域
- 新たに発生した問題
- source上確認できた構造的変化
- 主要Evidence references

単一の総合点だけで結論を表現しない。必要に応じて領域別countを示してよいが、countとqualityを混同しない。

## 2. Comparison Matrix

| Comparison ID | Stable Key | Area | Before Finding / Evidence | After Finding / Evidence | Visual Change | Structural Change | Status | Rationale | Confirmation |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

## 3. Domain Change Summary

Foundation / Styling、Component、Layout / Responsive、Interaction / State、Accessibility、UI Consistency / UX Structureごとに以下を記録する。

- Before state
- After state
- Change direction
- Evidence
- Remaining issue
- Constraint / uncertainty

## 4. Structural Change Log

見た目だけでは判別できない変更を優先して記録する。

- hardcoded → variable / token reference、またはその逆
- direct markup → shared component reuse、またはその逆
- duplicated CSS → shared style、または重複増加
- implicit variation → explicit variant、またはvariant proliferation
- page-local layout → shared composition、または過度なcoupling
- state / accessibility implementationの追加・削除・変更

## 5. Human Confirmation Required

- 比較条件の不一致
- runtimeでしか確認できない挙動
- source外asset / rules / business context
- intentional differenceか偶発的差異か
- Finding mappingの不確実性
- Design.md以外の変更要因

## Recommended Deliverable Names

- `before-audit.md`
- `after-audit.md`
- `comparison-report.md`
- 必要に応じて`comparison-matrix.csv`または`comparison-matrix.json`

Before / Afterの独立監査結果を保持し、Comparison Reportだけに観察事実を閉じ込めない。

# Comparative Audit Completion Criteria

- Comparison Audit Contractと条件差が記録されている
- Before / Afterへ同じPhase、criteria、粒度、Evidence requirementが適用されている
- 双方を独立観察してから差分判定している
- Stable KeyおよびFinding ID cross-referenceで追跡できる
- 全Comparable FindingにComparison Statusがある
- `Unchanged`と`Regressed`を省略していない
- visual changeとstructural changeを分離している
- source evidenceとruntime / visual evidenceを区別している
- Design.md Compliance Verificationを重複実施していない
- Design.md利用を理由にAfterを高評価していない
- 比較不能項目とHuman Confirmation Requiredを明示している
- 単一総合点ではなく領域別変化を報告している
- 変更の観察とDesign.mdへの因果帰属を区別している

# Full Audit Flow

```text
Audit Mode Selection
Mode A: Single Audit
Mode B: Comparative Audit → Comparison Audit Contract / Gate
        ↓
Phase A — Baseline Discovery
01 Scope
02 Frontend Architecture
03 Styling Architecture
04 Existing UI Assets
05 Component / Page Structure
        ↓
Phase A Review Gate
Human Review & Context Confirmation
        ↓
Phase B — UI Inventory
06 Foundation & Token Candidate Inventory
07 Components
08 Layout Inventory
09 Interaction / State
        ↓
Phase C — Analysis
10 Pattern
11 Divergence
12 UX / Design Review
        ↓
Phase D — Human Decision
13 Human Decision
        ↓
Phase E — Systemize
14 System Design
        ↓
Phase F — Verification
15 Verification

Mode B only:
Before / After Independent Findings
        ↓
Stable Mapping / Comparison Status
        ↓
Comparison Summary / Matrix / Structural Change Log
```

## Operational Note
この文書は、Clientから提供されたsource archive、Git repository、design assets、documentation等をAI Agentへ渡してAuditを実行する際の**監査手順・判断境界・出力基準**として使用する。

この文書自体はClient固有の正解を含めない。Client固有の背景・制約・意図がソースから確認できない場合、AI Agentは推測せず `Human Confirmation Required` とし、Human Reviewで補完する。

Comparative Auditにおいても、この原則は双方へ等しく適用する。BeforeとAfterの入力品質、source availability、runtime条件が異なる場合、その差を監査結果で隠さず、比較可能性の制約として残す。
