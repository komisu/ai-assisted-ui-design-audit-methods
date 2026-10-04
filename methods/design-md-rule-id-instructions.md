# Design.md 永続Rule ID付与・運用指示

添付した `Design.md` は、今後AIによるUI生成結果のCompliance
Verificationに使用します。

継続的な検証結果の比較・追跡を可能にするため、Design.md内の「実装時に遵守・判断すべきルール」に、永続的なRule
IDを付与してください。

今回の作業は単なる連番付与ではなく、今後Design.mdにルールの追加・編集・削除・統合・分割・並び替えが発生しても、過去のVerification結果との対応関係を維持できるRule
ID体系への移行を目的とします。

## 1. 目的

今後、Design.mdを参照して生成されたUIに対して、各ルールがどの程度反映されているかを検証します。

VerificationではRule ID単位で、

-   Pass
-   Partial
-   Fail
-   Not Defined
-   Human Confirmation Required

等の判定を行います。

Design.mdのVersionが更新されても、同じルールについて過去と現在の検証結果を追跡できることを最優先としてください。

## 2. Rule IDの基本原則

Rule
IDは、文書内の位置・章番号・見出し番号・表示順序とは独立した「永続識別子」としてください。

一度発行したRule IDは、原則として変更・再利用しません。

Design.mdの編集、章構成変更、ルール追加、並び替え等を理由として既存Rule
IDを振り直してはいけません。

例：

既存状態：

-   BTN-001
-   BTN-002
-   BTN-003

BTN-001とBTN-002の間に新しいルールが追加された場合：

-   BTN-001
-   BTN-004 ← 新規発行
-   BTN-002
-   BTN-003

としてください。

文書上の表示順とRule IDの番号順が一致する必要はありません。

## 3. Rule IDカテゴリー

以下を基本カテゴリーとしてください。

-   `CLR-001`：Color
-   `TYP-001`：Typography
-   `SPC-001`：Spacing
-   `RAD-001`：Radius
-   `BTN-001`：Button
-   `TAB-001`：Tab
-   `FRM-001`：Form
-   `TBL-001`：Table
-   `LAY-001`：Layout
-   `A11Y-001`：Accessibility
-   `SEM-001`：Semantic / Usage

既存Design.mdの構造上、これ以外のカテゴリーが必要な場合は独自判断で大量に追加せず、追加候補と理由を提示してください。

カテゴリー判断が難しいルールについては `Human Confirmation Required`
としてください。

## 4. Rule IDを付与する対象

Rule IDは、AIまたは実装者がUIを作成するときに、

-   守る必要がある
-   選択・判断する必要がある
-   実装結果から遵守・違反を検証できる
-   Compliance Verificationの判定対象になり得る

ルールに付与してください。

一方、以下には原則としてRule IDを付与しないでください。

-   背景説明
-   設計思想の説明のみの文章
-   参考情報
-   補足説明
-   Decision Log
-   変更履歴
-   Compliance判定そのものに使用しない文章

すべての文章に機械的にIDを付与することは禁止します。

## 5. Design.mdへの記載方法

Rule
IDは別ファイルだけで管理せず、Design.md本体の該当ルールに直接記載してください。

Design.mdをRule IDのSource of Truthとします。

既存Design.mdの見出し構造や人間が読む際の可読性を可能な限り維持し、Rule
IDのためだけに文書構造を大きく変更しないでください。

原則として以下のような形式を使用してください。

### Primary Button

**Rule ID: BTN-001**

Primary Buttonは主要な確定Actionに使用する。

複数の独立した検証可能ルールが同じセクション内に存在する場合は、それぞれにRule
IDを付与できます。

ただし、過度に細分化しないでください。

## 6. Rule ID Lifecycle

今後Design.mdを更新する際は、以下のルールを適用します。

### 6.1 文言修正

意味・判断基準が変わらない表現改善、誤字修正、説明追加の場合：

→ 既存Rule IDを維持する。

### 6.2 ルール追加

新しい独立ルールが追加された場合：

→ 新しいRule IDを発行する。

既存IDの間に挿入されても、既存IDを振り直さない。

### 6.3 ルール廃止

ルール自体が不要になった場合：

→ 既存Rule IDを `Deprecated` とする。

そのRule IDは永久に再利用しない。

### 6.4 ルール分割

1つのルールが、意味の異なる複数の独立ルールへ分割された場合：

→ 元Rule IDをDeprecatedとし、新しいRule IDをそれぞれ発行する。

旧Rule IDと新Rule IDの関係を記録する。

### 6.5 ルール統合

複数の既存ルールが、新しい1つのルールとして統合された場合：

→ 新しいRule IDを発行する。

統合元となった旧Rule IDはDeprecatedとし、対応関係を記録する。

### 6.6 ルールの意味変更

既存ルールの判断基準・適用条件・意味が実質的に変更された場合：

既存Rule IDを維持できる軽微な変更か、新Rule
IDを発行すべき変更かを独自判断せず、`Human Confirmation Required`
としてください。

## 7. Rule Registry

Design.md内に、Rule IDを管理する `Rule Registry` を設けてください。

最低限、以下を追跡できる構造にしてください。

  ------------------------------------------------------------------------
  Rule ID     Category    Status      Introduced   Last        Replaces /
                                                   Updated     Replaced By
  ----------- ----------- ----------- ------------ ----------- -----------

  ------------------------------------------------------------------------

Statusは最低限、

-   Active
-   Deprecated

を使用します。

現在のDesign.md
Versionが明確な場合は、そのVersionをIntroducedに記録してください。

過去Versionでいつ追加されたかソースから確認できない場合は推測せず、`Unknown`
または確認が必要であることを記録してください。

## 8. Verificationとの関係

今後の構成は以下を想定しています。

`Design.md` → Rule IDとルール内容のSource of Truth

`Design.md Compliance Verification.md` → Rule
IDをどのように検証・判定するかを定義する手順書

`design-compliance-report.md` → 実際の生成UIについてRule
ID単位で検証した結果

そのため、Rule
IDだけを見てDesign.mdのVersionをまたいで同一ルールを追跡できる状態を維持してください。

## 9. 重要な制約

今回の作業では以下を厳守してください。

-   既存Design.mdのルールの意味を変更しない
-   既存ルールを勝手に削除しない
-   新しいデザインルールを追加しない
-   曖昧なルールを推測で補完しない
-   Rule ID付与を理由にルール内容を書き換えない
-   Rule ID付与を理由に文書構造を必要以上に変更しない
-   既存ルールを過度に分割・統合しない
-   将来の追加を想定して既存IDを振り直さない
-   廃止IDを再利用しない
-   判断できない事項は推測せず `Human Confirmation Required` とする

## 10. 作業結果

最終的に以下を提示してください。

1.  永続Rule IDを付与したDesign.md
2.  Rule Registry
3.  新規発行したRule ID一覧
4.  Rule ID付与対象外と判断した主なセクション
5.  `Human Confirmation Required` 一覧
6.  ID体系・粒度について問題または追加提案がある場合、その内容

作業完了後、既存Design.mdのルール内容がRule
ID付与前後で意図せず変更されていないことも確認してください。
