# 自分のものにする

poteto-mode は一人のスタイルです。その下のプレイブック、ルーティング、モデルロールは、あなたのスタイルでも同じように機能します。このページでは、自分専用のモードを生成し、セッションから教訓を捕まえ、エージェントが同じミスを繰り返さないようにリポジトリを修正し、焦点の定まったスキルを書き、スキル変更を信頼する前にテストする方法を扱います。

思うより小さく始めてください。初日に多くのスキルは要りません。このプラグイン全体さえも要りません。普通にプロンプトし、エージェントがどこで失敗するかを見て、同じ失敗が 2 回現れたらスキルやチェックを追加してください。

## `/automate-me` で自分専用のモードを生成する

```text
/automate-me
```

スタイルを説明する必要はありません。[`/automate-me`](../../skills/automate-me/SKILL.md) があなたの履歴から読み取るからです。アクティブワークスペース内の最近のトランスクリプトから、返信、委譲、検証、コード、プロース、プロセスについての繰り返しの好みを掘り、どのパターンが本当にあなたらしいかを尋ねます。Cursor の組み込みの `create-skill` フローで `.cursor/skills/<your-name>-mode/SKILL.md` のドラフトを作り、[`/unslop`](../../skills/unslop/SKILL.md) を通し、ワークツリーから PR を開くので、他の変更と同じようにレビューできます。

習慣がずれたらまた実行してください。

```text
/automate-me update my mode skill with everything since its last edit
```

モード更新はスキルの最終変更以降の履歴のみを掘ります。あなたが反対しなかったルールは保ち、新しい証拠があるものは修正し、新しいパターンのためだけにセクションを追加します。

## `/reflect` でセッションの教訓を捕まえる

何かを教えてくれたタスクの直後に実行してください。

```text
/reflect that took way too long. capture what we learned so the next run doesn't repeat it.
```

[`/reflect`](../../skills/reflect/SKILL.md) はトランスクリプトを読み、将来のエージェントの作業を変える教訓だけを残し、提案を `Accepted`、`Rejected`、`Backlog` に仕分けます。スキルの変更前にはあなたの承認を待ちます。提案は、将来の決定を変える場合のみ承認してください。変な 1 セッションは逸話であって、ルールではありません。

## `/correct` で環境を修正する

同じミスで何度もエージェントを訂正するなら、修正は次のプロンプトではなくリポジトリに属します。保持される度合いで選択肢をランク付けします。

1. アーキテクチャやより良いデータ構造でミスを不可能にする。
2. 型、または修正を命名するエラーを持つ lint や CI チェックでブロックする。
3. テストで捕まえる。
4. ドキュメントやエージェントルールに書き留める。エージェントがルールを飛ばしても何も失敗しないので、これは最後。

人間のレビューはリストにありません。すべての PR で同じミスを捕まえなければならないレビュアこそが、これが修正する問題です。[`/correct`](../../skills/correct/SKILL.md) が作業を行います。

```text
/correct agents keep calling the database client directly instead of going through the repository layer
```

最近のコミット、revert、レビューコメント、回避策を説明するコメントを読み、ミスをクラスにグループ化します。クラスは 2 回起きたらカウントされます。最も頻繁なクラスをそれぞれ 1 コミットで、機能する最高のレベルで修正し、新しい各チェックが過去の実在のミスで失敗することを証明します。また、各ルールをそれを強制するものとペアにする表をエージェント指示ファイルに保ち、何も強制しないルールは繰り返しとして現れます。返信は各クラスを証拠、選ばれたレベル、より高いレベルが機能しなかった理由とともに列挙します。

引数なしで実行すると、履歴からクラスを自分で見つけます。`/reflect` と `/correct` は作業を分けています。`/reflect` は 1 セッションからスキルを改善します。`/correct` はミスクラスが戻ってこないようリポジトリを変えます。修正が新しい境界になるときは `/architect` と組み合わせてください。[多くのプロジェクトを並列に動かす](./07-overnight.md#run-many-projects-in-parallel) にはリポジトリ全体でその両方を行うプロンプトがあります。

## 焦点の定まったスキルを書く

捕まえたいワークフローがすでに分かっているときは次のようにします。

```text
/poteto-mode write a skill for verifying database migrations in this repo
```

スキルを書くことは [Authoring or modifying a skill プレイブック](../../skills/poteto-mode/playbooks/authoring-a-skill.md) にマッチし、Cursor の組み込みの `create-skill` 経由にルーティングし、frontmatter とリンクを検証し、Opening a PR プレイブック経由で結果を出荷します。エージェント向けのプロースは人間向けプロースより高いバーが要ります。役に立たない 1 文が、将来のエージェントが従う指示になるからです。`SKILL.md` をフリーハンドで書くのではなく、プレイブックにそのバーを保たせてください。

2 つの特殊ケースには独自の生成器があります。アプリを動かして挙動を証明しなければならないスキルは検証スキルなので、代わりに [`/create-verification-skill`](../../skills/create-verification-skill/SKILL.md) と [`/maintain-verification-skill`](../../skills/maintain-verification-skill/SKILL.md) を使ってください。モデル検査器、証明、または別の verifier でコードの性質をチェックするスキルは形式検証スキルなので、[`/create-formal-verification-skill`](../../skills/create-formal-verification-skill/SKILL.md) と [`/maintain-formal-verification-skill`](../../skills/maintain-formal-verification-skill/SKILL.md) を使ってください。[検証して出荷する](./06-verify-and-ship.md#prove-a-property-with-a-formal-verification-skill) が両方を扱います。

## `/technical-writing` で基準に沿ってドキュメントを書く

スキルだけがあなたが出荷するプロースではありません。ドキュメント、RFC、README、PR 説明、コミットメッセージには次のようにします。

```text
/technical-writing review the readme changes
```

[`/technical-writing`](../../skills/technical-writing/SKILL.md) は階層化された基準を適用します。ゴールは疲れたエンジニアが初回読みで理解できるプロースです。まずドキュメントのモード（チュートリアル、ハウツー、リファレンス、または解説）を選び、それから文ごとに作業します。誰が何をしているか、1 文に 1 つの思考、2 通りに読めないもの。あなたやエージェントが書いたばかりのものをレビューするのに使うか、ドキュメントを依頼する際に前もって命名してください。

## スキル変更をブラインドでテストする

スキルの編集はすべての将来のセッションに影響するので、それが実験であるかのようにテストしてください。他人のスキルを採用するときも同じです。それを保持する前に、自分の作業でエージェントを良くするか確認してください。

```text
/poteto-mode run the eval playbook on this skill change. same task for both variants, candidates stay blind.
```

スキルが外し続けていて、何をすべきか分かっているときは、1 つのタスクで変更してテストしてください。

```text
/poteto-mode update the review skill so it flags missing migrations, and eval the change.
```

前もって eval を求めることで編集を正直に保ちます。1 つの悪いセッションから書かれた修正はそのセッションに過適合しがちで、多くの編集を経るとスキルはドリフトします。eval は出荷前にドリフトを捕まえます。

[Eval プレイブック](../../skills/poteto-mode/playbooks/eval.md) は 1 つの失敗モード、オブザーバー効果を中心に構築されています。評価されていると知るエージェントは異なる挙動をします。だから候補エージェントはサニタイズされたディレクトリで有機的に見えるタスクを受け、「eval」や「candidate」の言葉はなく、互いの存在を知りません。1 人のジャッジが中立なラベル下ですべての出力を採点し、チェーン追従は候補が実際に何を主張したかではなく、どのファイルを実際に読んだかから採点されます。

ジャッジの判定を受け入れる前に、すべての出力を自分で読んでください。ジャッジに同意できないときは、自分の判断を疑う前にルーブリックを疑ってください。

## `/make-bot-ui` でボット UI を構築する

状況スキルの 1 つは Grok Bot ユーザー向けです。[`/make-bot-ui`](../../skills/make-bot-ui/SKILL.md) は、ボタンが webhook ルーチンを通してボットを起こす小さなページを構築します。例えば、レビューキューをスワイプし、各スワイプがボットにそのアイテムに対して行動するよう頼むことができます。あなたのマシン上のサーバが webhook の送信者キーを保持するので、キーはブラウザやチャットに届きません。スキルはページを Tailscale 上に公開することも扱います。

**落とし穴:** 挙動がおかしいからといってタスクの途中でスキルを編集しないでください。独自の PR で修正し、タスクを前に進めてください。機能作業に絡めて出荷されるスキル編集はレビューに不可視で、評価不能です。

次は [レシピと落とし穴](./10-recipes-and-pitfalls.md) です。
