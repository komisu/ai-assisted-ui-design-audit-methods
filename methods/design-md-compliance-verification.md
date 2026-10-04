# Design.md Compliance Verification

**Version:** 0.1\
**Purpose:** AIまたは実装者によって生成されたUI実装が、対象の
`Design.md` に定義されたルールをどの程度遵守しているかを、Rule
ID単位で再現可能な方法により検証する。

## 1. Purpose

本Verification Processは、`Design.md`
を参照して生成・実装されたUIについて、Design.mdに定義されたルールが実装結果にどの程度反映されているかを検証するための手順である。

本検証の目的は、単純な見た目の一致率やAI生成品質の評価ではない。

以下を明らかにすることを目的とする。

1.  Design.mdに明文化されたルールが正しく実装されているか
2.  AIまたは実装者がルールを適切に選択・適用できているか
3.  Design.mdの記述だけでは判断できない箇所が存在するか
4.  Design.md内に曖昧性・不足・矛盾が存在するか
5.  Design.mdの更新によって、継続的にComplianceが改善しているか

検証結果は、Design.md改善およびAIによるUI生成品質向上のためのEvidenceとして使用する。

## 2. Verification Inputs

検証時には、原則として以下を入力とする。

### Required

#### 2.1 Design.md

検証対象となるDesign RuleのSource of Truth。

原則として各検証可能ルールには永続的な `Rule ID` が付与されていること。

例： - `BTN-001` - `SPC-004` - `LAY-012` - `A11Y-007`

#### 2.2 Generated UI Implementation

Design.mdを参照して生成・実装されたUI。

可能な限り以下を含むこと。

-   Source Code
-   Component
-   CSS / Style
-   Token usage
-   UI structure
-   使用している既存Component / Variant
-   必要に応じて生成結果の画面またはPreview

#### 2.3 This Verification Process

本 `Design.md Compliance Verification.md`。

検証AIは本書に定義された手順・判定基準に従うこと。

## 3. Verification Principles

検証では以下を厳守する。

### 3.1 Evidence Based

判定は確認可能なEvidenceに基づいて行う。

可能な限り以下を記録する。

-   File path
-   Component name
-   Line / relevant code
-   CSS / Token
-   Variant
-   DOM / ARIA attribute
-   その他判定根拠

Evidenceが確認できない場合、推測でPassまたはFailとしてはならない。

### 3.2 No Assumption

Design.mdまたは実装結果から確認できない設計意図を推測しない。

「おそらくこういう意図だろう」「一般的にはこうする」「このUIなら普通はこうする」などの推測を判定根拠に使用しない。

### 3.3 No Automatic Improvement

Verification中に以下を行ってはならない。

-   Source Codeの修正
-   CSSの修正
-   Componentの置換
-   Design.mdの書き換え
-   新しいDesign Ruleの追加
-   ルールの統合・削除
-   UIの改善・リファクタリング

本工程は **Verification** であり、Improvement Phaseではない。

改善候補は記録できるが、実際の変更は行わない。

### 3.4 Rule ID Based Verification

原則としてDesign.mdのRule ID単位で検証する。

同一ルールを複数箇所で確認した場合は、Rule
IDとの対応関係を維持したままOccurrenceを記録する。

## 4. Verification Scope

最初に、Design.mdに存在するRule
IDについて、今回の生成UIへの適用可否を確認する。

各Ruleを以下のいずれかに分類する。

### Applicable

今回の生成UIに適用されるルール。Compliance Verificationの対象とする。

### Not Applicable

今回の生成UIには対象要素・状態・条件が存在せず、適用されないルール。Compliance
Rateの母数には含めない。

### Human Confirmation Required

Design.mdおよび生成物だけでは、そのRuleが今回適用されるか判断できない。人間による確認対象とする。

## 5. Rule Applicability

Compliance判定の前に、必ずApplicabilityを判定する。

Design.mdに存在するすべてのRule IDを自動的にCompliance
Rateの母数に含めてはならない。

例：

Design.mdに100個のRule
IDが存在し、今回のDashboardに適用されるRuleが65個の場合、Compliance
Verificationの基本母数は65とする。

残り35個は `Not Applicable` として記録する。

ただし、Applicability自体が判断できないRuleは
`Human Confirmation Required` とする。

## 6. Compliance Result Classification

Applicableと判断されたRuleについて、以下のいずれかで判定する。

### 6.1 Pass

Design.mdに定義されたRuleを、確認可能な範囲で満たしている。判定にはEvidenceを付与する。

### 6.2 Partial

Ruleの一部は満たしているが、一部が満たされていない。

または、同一Ruleが複数箇所に適用され、一部Occurrenceのみ違反している。

例： - Button Variantは正しいがSizeが規定外 -
5箇所中4箇所は正しいが1箇所だけ違反 - Stateの一部だけ実装されている

Partialの場合、満たしている部分と満たしていない部分を明記する。

### 6.3 Fail

Design.mdに明確なRuleが存在し、今回の実装に適用されるにもかかわらず、実装結果がそのRuleを満たしていない。Evidenceを必須とする。

### 6.4 Not Defined

生成UI上で設計判断が行われているが、その判断を評価するために必要なRuleがDesign.mdに存在しない。

これは実装違反ではない。Design.md側のCoverage Gap候補として扱う。

`Not Defined` は、既存Rule IDのPass /
Fail判定として無理に割り当ててはならない。

必要に応じて暫定的なFinding IDを付与する。例：`ND-001`

ただし、このIDはDesign.mdの正式Rule IDではない。

### 6.5 Human Confirmation Required

以下の場合に使用する。

-   Evidenceが不足している
-   実行時の状態を確認できない
-   Design.mdの記述だけでは判定できない
-   Ruleの適用条件が不明確
-   Design.md内に複数の解釈が成立する
-   Sourceだけでは確認できない
-   人間による設計判断が必要

推測によってPass / Partial / Failへ分類してはならない。

## 7. Occurrence Handling

1つのRule
IDが複数箇所に適用される場合、Rule単位の結果だけでなくOccurrenceも確認する。

例：

`BTN-003` が画面内の8個のButtonに適用される場合、

-   7 occurrences: Pass
-   1 occurrence: Fail

であれば、Rule Resultは原則 `Partial` とする。

違反OccurrenceについてEvidenceを記録する。

Occurrence数がSourceから確実に確認できない場合は推測しない。

## 8. Root Cause Classification

`Partial`、`Fail`、`Not Defined`、または判断困難なFindingについて、可能な範囲で原因を以下に分類する。

### 8.1 Implementation Failure

Design.mdには十分かつ明確なRuleが存在するが、実装がRuleを満たしていない。

### 8.2 Documentation Ambiguity

Design.mdにRuleは存在するが、記述が曖昧で複数の解釈が可能。

### 8.3 Documentation Gap

実装時に必要な判断についてDesign.mdにRuleが存在しない。主に
`Not Defined` と関連する。

### 8.4 Rule Conflict

Design.md内の複数Ruleが同じ状況に対して異なる判断を要求している、または同時に満たせない可能性がある。

### 8.5 Evidence Insufficient

Design.mdと提供された生成物だけでは原因を特定できない。

### 8.6 Human Decision Required

ルールだけでは解決できず、プロダクト固有の設計判断が必要。

原因を確定できない場合は推測せず、`Root Cause: Human Confirmation Required`
とする。

## 9. Critical Rules

すべてのRule違反を同じ重要度として扱わない。

ただし、Verification AIが独自に重要度を決定してはならない。

Design.mdまたは別途提供された定義で `Critical`
と明示されているRuleのみ、Critical Ruleとして扱う。

Critical指定が存在しない場合は、v0.1では独自のSeverity
Scoreを付与しない。

必要に応じて将来VersionでSeverity Modelを追加する。

## 10. Human Review

以下はHuman Review対象とする。

-   Human Confirmation Required
-   Documentation Ambiguity
-   Documentation Gap
-   Rule Conflict
-   Critical RuleのFail / Partial
-   AIの判定に十分なEvidenceがないもの
-   Rule IDの適用対象そのものに疑義があるもの

Human Review前に、AIがDesign.mdを修正してはならない。

## 11. Compliance Calculation

基本Compliance Rateは、Applicable Ruleのみを対象とする。

v0.1では、まず以下を個別に集計する。

-   Applicable Rules
-   Pass
-   Partial
-   Fail
-   Human Confirmation Required
-   Not Applicable
-   Not Defined Findings

基本Compliance Rateは以下とする。

`Pass Rules / Applicable Rules × 100`

ただし、この数値だけで生成品質を評価してはならない。

必ず以下も併記する。

-   Partial数
-   Fail数
-   Critical Rule Violations
-   Human Confirmation Required数
-   Not Defined Findings数

Partialを0.5点等として換算するWeighted Scoreはv0.1では使用しない。

## 12. Design.md Coverage Findings

生成UIを検証する過程で、Design.mdに存在しない設計判断を発見した場合、`Not Defined Finding`
として記録する。

例：

  Finding ID   UI Decision            Evidence        Existing Rule   Status
  ------------ ---------------------- --------------- --------------- -------------
  ND-001       KPI Card内の数値配置   `KpiCard.tsx`   None            Not Defined

Not Defined
Findingを発見したからといって、新しいRuleを自動追加してはならない。

Design.mdへの追加要否はHuman Reviewで決定する。

## 13. Design.md Improvement Candidates

Verification結果からDesign.md側の改善候補が見つかった場合、以下として記録できる。

-   Clarification Candidate
-   Missing Rule Candidate
-   Conflict Resolution Candidate
-   Example Addition Candidate
-   Exception Definition Candidate

ただし、ここでは改善案を確定Ruleとして扱わない。

また、Design.mdを直接変更しない。

## 14. Output Format

最終成果物として `design-compliance-report.md` を作成する。

最低限、以下を含める。

### 14.1 Verification Metadata

-   Verification Date
-   Design.md Version
-   Verification Process Version
-   Generated UI / Target
-   Verification AI / Environment（確認可能な場合）

### 14.2 Executive Summary

以下を簡潔に記載する。

-   Applicable Rules
-   Pass
-   Partial
-   Fail
-   Compliance Rate
-   Critical Rule Violations
-   Not Defined Findings
-   Human Confirmation Required

### 14.3 Rule Verification Results

  --------------------------------------------------------------------------------
  Rule ID     Applicability   Result      Evidence    Root Cause       Human
                                                                       Review
  ----------- --------------- ----------- ----------- ---------------- -----------
  BTN-001     Applicable      Pass        `...`       ---              No

  SPC-004     Applicable      Fail        `...`       Implementation   No
                                                      Failure          

  LAY-012     Applicable      Partial     `...`       Documentation    Yes
                                                      Ambiguity        

  FRM-003     Not Applicable  ---         No form     ---              No
                                          exists                       
  --------------------------------------------------------------------------------

### 14.4 Not Defined Findings

Design.mdにRuleが存在しない設計判断を記録する。

### 14.5 Human Confirmation Required

人間による判断が必要な項目を一覧化する。

### 14.6 Design.md Improvement Candidates

Verificationから発見されたDesign.md側の改善候補を記録する。

### 14.7 Conclusion

以下を区別してまとめる。

-   Design.mdが明確で実装も遵守された領域
-   Design.mdは明確だが実装が遵守されなかった領域
-   Design.mdの記述に曖昧性がある領域
-   Design.mdに定義が不足している領域
-   人間による確認が必要な領域

## 15. Comparison Across Versions

過去のVerification Reportが提供されている場合、Rule
IDをキーとしてVersion間比較を行うことができる。

例：

  Rule ID   Design.md v4.15   v4.16     v4.17
  --------- ----------------- --------- -------
  BTN-001   Fail              Pass      Pass
  SPC-004   Partial           Partial   Pass
  LAY-012   Not Defined       Pass      Pass

ただし、比較対象となるUI・実装条件・Design.md
Versionが異なる場合、その差を明記する。

異なる条件の結果を単純に品質向上・低下と断定してはならない。

Deprecated Rule、新規Rule、分割・統合されたRuleについてはRule
Registryを参照し、対応関係を確認する。

## 16. Verification Rules for AI

Verificationを実行するAIは以下を厳守すること。

1.  Design.mdをSource of Truthとして扱う。
2.  一般的なUIベストプラクティスをDesign.mdの代わりに使用しない。
3.  Design.mdに存在しないルールを作らない。
4.  Design.mdの曖昧な記述を独自解釈で補完しない。
5.  EvidenceなしでPass / Partial / Failを断定しない。
6.  Applicabilityを確認してからCompliance判定を行う。
7.  Not Applicable RuleをCompliance Rateの母数に含めない。
8.  Not DefinedをImplementation Failureとして扱わない。
9.  Human Confirmation Requiredを無理にPass / Failへ分類しない。
10. Verification中にSource Codeを変更しない。
11. Verification中にDesign.mdを変更しない。
12. 改善案と確認された事実を混同しない。
13. Rule IDを変更・再採番しない。
14. Deprecated Rule IDを再利用しない。
15. 複数Occurrenceがある場合、一部のPassだけを見てRule全体をPassにしない。
16. Design.md内に矛盾を発見した場合、一方を勝手に正しいと判断しない。
17. Sourceから確認できない制作意図を推測しない。
18. Human Reviewが必要な事項を明示する。
19. Verification結果からDesign.mdを自動修正しない。
20. Phase完了時点で、事実・判定・推測・改善候補が混在していないことを確認する。

## 17. Exit Criteria

Verificationは以下を満たした時点で完了とする。

-   Design.mdのRule ID一覧を確認した
-   各RuleのApplicabilityを確認した
-   Applicable RuleについてCompliance判定を実施した
-   Pass / Partial / Failに可能な限りEvidenceを付与した
-   Not Defined Findingsを分離した
-   Human Confirmation Requiredを分離した
-   Root Causeを推測で確定していない
-   Design.md Improvement Candidatesを実際のRule変更と分離した
-   Compliance Summaryを作成した
-   Design.mdおよびSource Codeを変更していない
-   最終成果物 `design-compliance-report.md` を作成した

## 18. v0.1 Scope Note

本Versionでは、Verification
Process自体の妥当性を検証することを優先する。

以下は意図的にv0.1の対象外とする。

-   PartialへのWeighted Score付与
-   Ruleごとの自動Severity判定
-   AIによるDesign.md自動修正
-   Verification結果による自動Rule生成
-   複雑な品質スコアリング
-   異なるUI間の単純な優劣比較

実際のVerification運用から得られた課題をもとに、必要に応じてv0.2以降で拡張する。
