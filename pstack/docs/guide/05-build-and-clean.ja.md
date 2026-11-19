# 変更を作り、差分をきれいにする

ビルドのプレイブックは 1 つの規律を共有しています。あなたが観察したことを言い、プレイブックに証拠を要求させることです。このページでは、よくあるビルドタスクごとにプロンプトに何を入れるか、そして差分をレビュー可能に保つクリーンアップの習慣を示します。

## 各ビルドプレイブックに知っていることをプロンプトする

バグのプロンプトは症状を述べ、まず再現を求めます。

```text
/poteto-mode this command emits two records after a retry. repro first, then fix and verify.
```

機能のプロンプトは挙動と変えてはならないことを述べます。

```text
/poteto-mode add a --json flag. text output stays byte-identical. verify both forms.
```

リファクタリングのプロンプトは構造が動く前に挙動を固定します。

```text
/poteto-mode move parsing into one module, zero behavior change. record the current output first and prove it's unchanged after.
```

パフォーマンスのプロンプトは印象ではなく測定を述べます。

```text
/poteto-mode startup takes 1.8s on this fixture. trace it, fix the measured cause, show me before and after.
```

これらはそれぞれのプレイブック（[Bug fix](../../skills/poteto-mode/playbooks/bug-fix.md)、[Feature](../../skills/poteto-mode/playbooks/feature.md)、[Refactoring](../../skills/poteto-mode/playbooks/refactoring.md)、[Perf issue](../../skills/poteto-mode/playbooks/perf-issue.md)）にルーティングされ、プレイブックはあなたが書かなかったステップを補います。修正前の再現、実装前のデータ形状の命名、再構築前の挙動固定、最適化前のプロファイリングです。

1 つの数値の持続的な改善には [Hillclimb プレイブック](../../skills/poteto-mode/playbooks/hillclimb.md) があります。メトリクス、ターゲット、試行回数の下限を与えれば、凍結した測定ハーネスで仮説を 1 つずつループします。勝ちは保持し、それ以外はすべて revert します。

パフォーマンスの両プレイブックは数値に対して [`/benchmark-checklist`](../../skills/benchmark-checklist/SKILL.md) を実行します。Perf issue はベースラインとその後のすべての数値を検査し、Hillclimb は凍結前にハーネスを検査します。自分で打つべきときは [検証して出荷する](./06-verify-and-ship.md#vet-a-measured-number-with-benchmark-checklist) を見てください。

修正前に原因を知りたいこともあります。リーク、アイドル CPU のスピン、ビジュアルグリッチなどのライブ症状には、[Runtime forensics プレイブック](../../skills/poteto-mode/playbooks/runtime-forensics.md) が実行中のプロセスを計測します。すでにキャプチャしたプロファイルには、[Trace forensics プレイブック](../../skills/poteto-mode/playbooks/trace-forensics.md) がアーティファクトを読み、ホットフレームをソースにマップします。どちらも修正ではなく診断を返します。

```text
/poteto-mode here's a cpuprofile from the slow startup. tell me where the time goes and which source lines own it. no fix yet.
```

## `/tdd` で失敗するテストを先に書く

バグに安価なローカルテストパスがあるとき、プロンプト全体を 2 語にできます。

```text
/tdd implement
```

文脈があれば、それで十分です。[`/tdd`](../../skills/tdd/SKILL.md) は意図した理由で失敗する最小のテストを書き、次に修正を書き、テストを再実行します。テストに広範なハーネス設定や脆いモックが必要になる場合、スキルはそう述べ、最も近い実行可能なチェックを代わりに使います。実コマンドの方が強い証拠になるところにテストを押し付けないでください。

## TypeScript ルールを名前でロードする

[`typescript-best-practices`](../../skills/typescript-best-practices/SKILL.md) は型システムの原則を具体的なルールに変えます。discriminated union、境界での `unknown`、網羅的なバリアント、スキーマ由来の型などです。自動ではロードされないので、タスクが `.ts` または `.tsx` ファイルに触るときは `/typescript-best-practices` と入力してください。

## コミット前にクリーンアップする

[Opening a PR プレイブック](../../skills/poteto-mode/playbooks/opening-a-pr.md) は各コミット前に差分に `/deslop` を実行し、poteto-mode は PR 説明とコミット本文に [`/unslop`](../../skills/unslop/SKILL.md) を適用します。`/deslop` は `cursor-team-kit` プラグインに同梱されており、pstack には含まれません。持っていない場合は、同じ結果を平易な言葉で要求してください。ナレーション的コメント、根拠のないガード、死んだ互換パス、無関係な編集を取り除きます。

プロースには、`/unslop` がターゲットと追加ルールを受け取ります。

```text
/unslop the readme changes, no emdashes
```

あなた独自の短縮形ができるでしょう。スキルは `unslop that, tighten it` のような簡潔なプロンプトからも意図をうまく読み取ります。

## `/no-comments` でコメントを剥がす

コメントには独自のパスが必要で、それを書いたエージェントが行うべきではありません。作者は、あなたが自分のコメントを守るのと同じようにコメントを守ります。だからレビュー前に、新鮮な目に渡してください。

```text
/no-comments the diff
```

[`/no-comments`](../../skills/no-comments/SKILL.md) は [Comment Sicko](../../agents/comment-sicko.md) を起動します。これは短い keep リストを持つ読み取り専用のレビュアです。ライセンスヘッダ、パブリック API のドックコメント、コードが表現できないことを説明するリンク、形を変えられない外部依存によって強制される挙動です。それ以外はすべて消えます。あなた自身のコードの驚きはパスをもらえません。コメントはリファクタのフラグとして戻り、`/no-comments` は受け入れたフラグを根本原因で修正します。コメントが「削除しないで」などの制約を主張する場合、スキルはその主張を型、テスト、または lint にエンコードすることを提案します。いずれにせよ、コメントは出ます。

分業は明確にしておく価値があります。`/deslop` はコードからスロップをきれいにし、`/unslop` はプロースからきれいにし、`/no-comments` はコメントをそれを書かなかったレビュアに渡します。

**落とし穴:** クリーンアップは任意の仕上げではありません。ナレーションコメントと防衛的な死にコードを含む差分は、レビュアには未完成に読め、余分なコードは次のバグが隠れる場所です。差分が水増しに感じたら、レビューに指摘される前に、コミット前に `deslop it` と言ってください。

次は [検証して出荷する](./06-verify-and-ship.md) です。
