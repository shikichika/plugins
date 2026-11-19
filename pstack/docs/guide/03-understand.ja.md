# 変更する前にコードを理解する

理解していないコードを編集することは、微妙なリグレッションを生む原因であり、それはエージェントについてもあなたについても同じです。エージェントは通常、2 つの理由で失敗します。あなたの要求を誤読するか、作業を正しく行うコンテキストを持たないかです。[プロンプトに入れるもの](./02-poteto-mode.md#what-goes-in-a-prompt) が前者に対処します。このページは後者に対処します。

pstack には 4 つの入り口があります。`/how` はコードが今何をしているかを説明します。`/why` はその形になった理由を掘り出します。`/teach` は両方を 1 つの説明に織り込みます。`/recall` はあるトピックに関するあなた自身の最近のコンテキストを再構築します。それぞれが、あなたがチェックできる言葉でエージェントに自己説明させます。それが、あなたよりコードに詳しいかもしれないエージェントを監督する方法です。

![探偵が虫眼鏡で機械の設計図を調べ、ロボットがケースファイルを取ってくる。背後の証拠ボードは /how と /why の下に手がかりを結びつけている。](./images/understanding.jpg)

## 読み取り専用の調査から始める

原因が不明なときは、修正ではなく調査結果を求めてください。

```text
/poteto-mode investigate why background jobs time out every few hours. give me what we know, what data you used, and your best hypotheses. don't change any code yet.
```

「don't change any code yet」はこれを [Investigation プレイブック](../../skills/poteto-mode/playbooks/investigation.md) にルーティングします。コードをトレースし、動機に関する質問には `/why` を加え、引用付きの説明を返します。選択肢の比較の場合は、トレードオフ表付きの推奨を返します。「どのデータを使ったか」と尋ねることで、エージェントは証拠と推測を分けます。調査結果が修正を指すときは、新しいタスクとして修正を始めます。

## `/how` で挙動をトレースする

```text
/how do we dedupe notifications? is there an n+1 when we look up subscribers?
```

実際に抱えている質問をしてください。[`/how`](../../skills/how/SKILL.md) はコードを読み、シニアエンジニアがサブシステムにあなたをオンボーディングするレベルで、ランタイムフロー、主要な型、非自明な部分を含めて回答します。コードを探索して説明を書く読み取り専用のサブエージェントを 1 つ起動します。

## `/why` で歴史を掘る

```text
/why was the retry limit set to five? does the reason still hold?
```

[`/why`](../../skills/why/SKILL.md) はコードではなく記録から回答します。コードを導入したコミットと PR 議論、その後の各変更を読み、その痕跡が薄いときは他のソースも見ます。回答は作者が書いたものを引用し、エージェント自身の推論は推論として示し、記録が答えていないことも述べます。「誰も理由を書き残さなかった」こと自体が答えだからです。

2 つは自然に組み合わさります。歴史が混乱を説明していると疑うときは、`do why first then how` が良いプロンプトです。

## `/teach` で実際に理解する

```text
/teach me how this PR changes retries. convince me it fixes the cause and not the symptom.
```

[`/teach`](../../skills/teach/SKILL.md) は要約では足りないときのためのものです。`/how` と `/why` を実行し、小さな変更なら片方だけでも、それらを図ごとに組み立てる平易な説明に織り込みます。「convince me」というフレームは再利用する価値があります。説明をツアーではなくあなたがテストできる議論に変えるからです。

エージェント自身の選択にも使えます。

```text
/teach me why you implemented it this way and not with a queue. what did you trade off, and why?
```

教えることはあなたと同じくらいエージェントの助けにもなります。作業を説明しなければならないエージェントは、自信をもって断定して進むのではなく、コードを読み、各主張を証拠で裏付けなければなりません。

## `/recall` で自分のコンテキストを再構築する

```text
/recall catch me up on the export work from last week
```

[`/recall`](../../skills/recall/SKILL.md) はあなた自身の最近のチャットと共有記録（イシュー、過去の修正、まだ発生中のエラー）を掘り、現状と次の手順に関する短い報告を返します。古いチャットには新鮮なエージェントが持たないコンテキストがあるので、古いトピックに関する新しい作業は、まずそれをロードしてから新しい入力を渡してください。

```text
/recall my work on the virtualized list from yesterday, then read this bug report.
```

1 つの特定のチャットを再開したい場合は、下の Session pickup プレイブックが該当し、`/recall` ではありません。

## Session pickup で前の作業を引き継ぐ

別のエージェント（または先週のあなた）が中途半端にブランチを残した場合は次のようにします。

```text
/poteto-mode take over this branch. read the decision log, figure out what's done, and continue from there. don't redo finished work.
```

[Session pickup プレイブック](../../skills/poteto-mode/playbooks/session-pickup.md) は前の痕跡を正とします。ブランチの状態と決定を再構築し、再開点を命名し、継承された主張を元のゴールに照らして検証します。ゼロから再導出するのではありません。

**落とし穴:** 「どうせエージェントがコードを読むから」といってこのページのスキルを飛ばさないでください。トレースしたモデルなしで編集を始めたエージェントは、最初にもっともらしい箇所で症状を直しがちです。`/how` を先にやる方が、2 つ目のバグより安上がりです。

次は [変更を設計する](./04-design.md) です。
