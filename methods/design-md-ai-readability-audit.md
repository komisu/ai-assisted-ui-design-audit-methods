# Design.md AI Readability Audit

添付された `Design.md`
を、**AIエージェントがUI実装時の設計ルールとして参照する文書**という観点から監査してください。

この監査の目的は、Design.mdを短くすることや、特定の外部仕様へ準拠させることではありません。

目的は、AIがDesign.mdを参照した際に、

-   必要なルールを正しく発見できる
-   同じ判断について複数箇所を探さなくてよい
-   ルール同士を誤って解釈しない
-   実装時に独自判断や推測を必要以上に行わない
-   既存のデザインシステムを一貫して再現できる

状態になっているかを検証することです。

------------------------------------------------------------------------

## Audit Rules

### 1. 文書量だけで評価しない

行数が多いこと自体を問題としないでください。

「500行以内」「1000行以内」など、根拠のない行数制限を評価基準に使用しないでください。

文書量ではなく、

-   情報探索性
-   一貫性
-   重複
-   ルールの局所性
-   判断の明確性

を評価してください。

### 2. Rule Locality

同じ対象に関するルールが複数箇所へ不必要に分散していないか確認してください。

分散している場合は、

-   意図的な参照関係
-   不必要な重複
-   矛盾の可能性

を区別してください。

### 3. Single Source of Truth

同じ値・ルール・判断について、複数の定義元が存在していないか確認してください。

特に以下を確認してください。

-   Color
-   Spacing
-   Typography
-   Radius
-   Border
-   Shadow
-   Component size
-   Layout
-   Responsive behavior
-   State
-   Accessibility

同じ内容が複数箇所に存在する場合は、どこをSource of
Truthにするのが自然か示してください。

### 4. Exact Value / Rule / Principle の分離

記述を以下の3種類に分類できるか確認してください。

**Exact Value**\
具体的な実装値。

**Rule**\
その値・Component・Variantをいつ使用するか。

**Principle**\
未定義ケースで判断するときの優先原則。

これらが混在してAIが判断しにくくなっている箇所を指摘してください。

### 5. Layout Rules

Layoutに関する記述を重点的に確認してください。

以下が必要に応じて判断できる状態か確認してください。

-   Application shell
-   Page structure
-   Content width
-   Grid / Flexの基本方針
-   Section spacing
-   Component間spacing
-   Alignment
-   Container
-   Table / Data Grid
-   Form layout
-   Dashboard layout
-   Responsive behavior

CSSの実装詳細を大量に列挙する必要はありません。

AIが「この画面をどう組み立てるべきか」を判断できる情報が存在するかを評価してください。

### 6. Component Rules

各Componentについて、必要に応じて以下が判断できるか確認してください。

-   Purpose
-   Usage
-   Variant
-   Size
-   Layout
-   State
-   Interaction
-   Accessibility
-   Do / Don't
-   Exception

すべてのComponentにすべての項目を要求しないでください。

不足によってAIが推測する可能性が高い場合のみ指摘してください。

### 7. Ambiguity

AIによって解釈が分かれそうな表現を抽出してください。

特に、

-   基本的に
-   原則
-   必要に応じて
-   適切に
-   十分な
-   場合によって
-   なるべく
-   推奨

などの表現について、その後に判断条件が存在するか確認してください。

曖昧語そのものを禁止しないでください。

判断条件が不足している場合のみ問題として扱ってください。

### 8. Exceptions

例外ルールが通常ルールを壊していないか確認してください。

例外がある場合、

**Default → Exception → Condition**

の関係が読み取れるか確認してください。

### 9. Accessibility

アクセシビリティルールが独立した章に存在するだけでなく、必要なComponentの実装判断につながっているか確認してください。

特に、

-   focus-visible
-   keyboard interaction
-   semantic state
-   selected / active state
-   disabled state
-   contrast
-   icon-only controls
-   ARIA

について、Component固有ルールとの関係を確認してください。

### 10. Contradictions

文書全体を横断して、明示的または潜在的な矛盾を探してください。

値だけでなく、UsageやBehaviorの矛盾も確認してください。

### 11. AI Implementation Risk

最終的に、このDesign.mdを読んだAIが新規UIを実装すると仮定してください。

その際、

-   独自の値を作りそう
-   Variantを誤って選びそう
-   Componentを新規作成しそう
-   Layoutを勝手に判断しそう
-   Accessibilityを落としそう
-   例外をDefaultとして扱いそう

な箇所を特定してください。

### 12. Missing Rules / Existing Implementation Investigation

Design.mdに必要な情報が存在しない場合、**ただちに新しいデザインルールの追加を提案しないでください。**

Design.mdに記載がないことと、プロダクトにルールが存在しないことは同義ではありません。

既存ソースコード、CSS、Component、Storybook、既存画面等に、すでに共通パターンや実装ルールが存在している可能性があります。

不足を発見した場合は、以下の順序で扱ってください。

**Step 1 --- Design.mdを確認**\
既存の定義・参照・例外が本当に存在しないか確認する。

**Step 2 --- Existing Implementation Investigation Required**\
Design.mdだけでは判断できない場合、新しいルールを提案せず、

`Existing Implementation Investigation Required`

と分類する。

その際、何を調査すべきかを具体的に示してください。

例：

-   既存画面の `.wrap` / containerのmax-width
-   Card / StatTile配置時のgrid・gap
-   Tableの狭幅時の挙動
-   Chartのaxis / legend / tooltip実装
-   Tooltipのpadding / max-width / interaction
-   Componentの既存Variant
-   breakpointの使用状況

**Step 3 --- Existing Pattern Found**\
既存実装に一貫したパターンが確認できた場合のみ、

「Design.mdへのルール反映候補」

として報告する。

この段階でもDesign.mdを直接変更しない。

**Step 4 --- No Existing Pattern Found**\
既存実装を調査しても共通パターンが存在しない場合は、

`Human Decision Required`

とする。

AI自身で新しい値、ルール、breakpoint、layout
pattern、Component仕様を決定しない。

#### Missing Rule Classification

Design.md上の不足を発見した場合は、以下のいずれかに分類してください。

-   **Documentation Gap** ---
    既存ルールは確認できるがDesign.mdに記載されていない
-   **Existing Implementation Investigation Required** ---
    Design.mdだけでは判断できず、既存実装の調査が必要
-   **Human Decision Required** ---
    調査しても共通ルールが存在せず、設計判断が必要
-   **Out of Scope** ---
    Design.mdで定義する必要がない、または対象プロダクトでは扱わない

「Missing」であることだけを理由に、新しいルールの作成をRecommendationに含めないでください。

------------------------------------------------------------------------

# Output

Design.mdは変更しないでください。

まず監査結果のみを出してください。

## 1. Executive Summary

Design.md全体について、

-   構造
-   情報探索性
-   一貫性
-   AI実装時の解釈リスク

を簡潔にまとめてください。

## 2. Findings

各指摘を以下の形式で記述してください。

### Finding XX --- タイトル

**Category:**\
Rule Locality / Duplication / Ambiguity / Contradiction / Layout /
Component / Accessibility / Structure / Other

**Severity:**\
High / Medium / Low

**Evidence:**\
該当する見出し、記述、可能であれば行番号。

**Current:**\
現在どのように記述されているか。

**Risk:**\
AIがどのように誤解・誤実装する可能性があるか。

**Recommendation:**\
どのように整理するとよいか。

## 3. Duplicate / Distributed Rules

同一または類似ルールが複数箇所に存在するものを一覧化してください。

単なる関連記述は重複として扱わないでください。

## 4. Potential Contradictions

矛盾している、または条件が不足しているため矛盾して見える記述を一覧化してください。

## 5. Layout Coverage

以下について、

-   Clear
-   Partial
-   Missing
-   Not Applicable

で評価してください。

Application shell\
Page structure\
Content width\
Spacing\
Grid\
Alignment\
Dashboard\
Table / Data Grid\
Form\
Responsive behavior

評価には必ずEvidenceを付けてください。

## 6. AI Interpretation Risks

このDesign.mdを参照してAIが新しい画面を1画面実装すると仮定し、特に判断に迷いそうなポイントを最大10件挙げてください。

## 7. Recommended Structural Changes

必要な場合のみ、

-   移動
-   統合
-   分離
-   参照化
-   追記

を提案してください。

既存ルールそのものを勝手に変更しないでください。

------------------------------------------------------------------------

# Important Constraints

-   Design.mdを直接修正しない
-   実装コードを変更しない
-   存在しないデザインルールを新しく作らない
-   一般的なベストプラクティスだけを理由に既存ルールを否定しない
-   行数だけを理由に文書を短縮しない
-   Google DESIGN.md / Stitchへの準拠を要求しない
-   ソースから判断できないものは推測しない
-   不明な場合は `Human Confirmation Required` とする
-   Missingを発見しても、それだけを理由に新規ルールを提案しない
-   既存実装の調査が必要な場合は
    `Existing Implementation Investigation Required` とする
-   既存実装にも共通パターンが存在しない場合は `Human Decision Required`
    とする
-   「よりモダンだから」「一般的だから」という理由だけで変更を提案しない

この監査では、**Design.mdの内容そのものの好みではなく、Design.mdがAIにとって一貫した実装仕様として機能するか**を評価してください。
