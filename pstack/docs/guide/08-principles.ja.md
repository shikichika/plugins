# 原則の名前で舵を取る

pstack は 24 の原則を個別のスキルとして同梱しています。`/poteto-mode` は複数ステップのタスクの最初にそのインデックスを読み、タスクがトリガーしたものを適用し、返信で各適用された原則を、それが変えた決定とともに命名します。

原則を呼び出すのではありません。その名前を使って舵を取ります。各名前は、エージェントがすでに読んだ完全なルールを指すので、1 つのフレーズが段落の指示より正確に作業を方向転換します。

## 実際に舵を取る

エージェントがすでにある 3 つのアダプタに新しいアダプタを付け足そうとしているとします。

```text
use subtract before you add. delete the obsolete adapters first, then design what's left.
```

ビルドが通ったから成功だと主張しているとします。

```text
apply prove it works. run the real import flow and show me the written records.
```

2 つの並列試行が同じブランチに書き込もうとしているとします。

```text
separate before serializing shared state. give each attempt its own worktree, no locks.
```

各フレーズが効くのは、背後のルールが具体的だからです。エージェントはそれでも返信で、ルールが変えた決定を述べなければなりません。背後の決定なしの原則引用は、エージェントがルールを命名しただけで適用しなかったことを意味します。

## 24 の原則、簡潔に

コア原則は、どれだけ作り、いつ設計を再考するかを決めます。

- [Laziness Protocol](../../skills/principle-laziness-protocol/SKILL.md) は削除と、問題を解決する最小の変更を優先する。
- [Foundational Thinking](../../skills/principle-foundational-thinking/SKILL.md) はロジックを書く前にコアデータ構造を選ぶ。
- [Redesign from First Principles](../../skills/principle-redesign-from-first-principles/SKILL.md) は新しい要件を、初日からそこにあったかのように統合する。
- [Attack the Premise](../../skills/principle-attack-the-premise/SKILL.md) は 2 回以上失敗した修正が共有していた前提を書き出し、次の修正の前に直接テストする。
- [Subtract Before You Add](../../skills/principle-subtract-before-you-add/SKILL.md) は上に積む前に死に重りを取り除く。
- [Minimize Reader Load](../../skills/principle-minimize-reader-load/SKILL.md) は読み手が頭に抱えなければならない層と隠れた状態を畳む。
- [Outcome-Oriented Execution](../../skills/principle-outcome-oriented-execution/SKILL.md) は使い捨ての互換状態を保存するのではなく、書き直しを目標設計に収束させる。
- [Experience First](../../skills/principle-experience-first/SKILL.md) は実装の都合よりユーザーの結果を選ぶ。
- [Exhaust the Design Space](../../skills/principle-exhaust-the-design-space/SKILL.md) は前例がないとき、2〜3 の競合するプロトタイプを作る。
- [Build the Lever](../../skills/principle-build-the-lever/SKILL.md) は作業を行うか証明するスクリプトを作り、レビュアが再実行できるようにする。エージェントが同じことを手でやり続けるなら、持っていてほしい道具やスキルを書かせる。スクリプトがステップを毎回同じ方法で実行できるなら、スクリプトを使い、判断にはエージェントを取っておく。

アーキテクチャ原則は、状態、検証、互換性がどこに住むかを決めます。

- [Model the Domain](../../skills/principle-model-the-domain/SKILL.md) は繰り返されるルールを散乱した条件分岐ではなく 1 つの構造にエンコードする。
- [Boundary Discipline](../../skills/principle-boundary-discipline/SKILL.md) は境界で検証し、内部の型を信頼する。
- [Type System Discipline](../../skills/principle-type-system-discipline/SKILL.md) は不正な状態を表現不能にする。
- [Make Operations Idempotent](../../skills/principle-make-operations-idempotent/SKILL.md) はリトライを同じ終状態に収束させる。
- [Migrate Callers Then Delete Legacy APIs](../../skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) は呼び出し元を移行してレガシーを 1 波で削除する。
- [Separate Before Serializing Shared State](../../skills/principle-separate-before-serializing-shared-state/SKILL.md) は調整を加える前に共有を取り除く。

検証原則は何が証明に値するかを定義します。

- [Prove It Works](../../skills/principle-prove-it-works/SKILL.md) はプロキシではなく実アーティファクトを検証する。
- [Fix Root Causes](../../skills/principle-fix-root-causes/SKILL.md) はコードを変更する前に再現し、原因までトレースする。
- [Sequence Work into Verifiable Units](../../skills/principle-sequence-verifiable-units/SKILL.md) は次を始める前に、小さなユニットそれぞれをチェックで締めくくる。
- [Test Behavior, Not Implementation](../../skills/principle-test-behavior-not-implementation/SKILL.md) はコードのユーザーが観測できるものをアサートするので、その挙動が壊れるとテストが失敗し、内部変更には耐える。
- [Explain the Number](../../skills/principle-explain-the-number/SKILL.md) は、誰かがその数値を信頼または報告する前に、何が測定された数値を制限するかを命名し、別のものを測定していないことを排除する。[`/benchmark-checklist`](../../skills/benchmark-checklist/SKILL.md) はそれを実行から答える 7 つの質問にします。

委譲原則は並列作業を正気に保ちます。

- [Guard the Context Window](../../skills/principle-guard-the-context-window/SKILL.md) は大量の読み取りをサブエージェントにルーティングし、発見をメインチャットに保つ。
- [Never Block on the Human](../../skills/principle-never-block-on-the-human/SKILL.md) は可逆的な作業で進み、結果を提示する。

そしてメタ原則が 1 つ。

- [Encode Lessons in Structure](../../skills/principle-encode-lessons-in-structure/SKILL.md) は 2 度繰り返したアドバイスを lint、チェック、スクリプトに変える。[`/correct`](../../skills/correct/SKILL.md) は [自分のものにする](./09-make-it-yours.md#fix-the-environment-with-correct) で示されているように、リポジトリ全体に適用する。

リストを暗記する必要はありません。今ざっと目を通し、ここの名前なら防げたことをエージェントがしているのを見つけたら戻ってきてください。それが語彙を定着させる方法です。

次は [自分のものにする](./09-make-it-yours.md) です。
