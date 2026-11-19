---
name: poteto-help
description: pstack のセットアップ、/poteto-mode、タスクに適したスキル・プレイブック・原則の選び方をユーザーに案内します。質問とともに /poteto-help と入力してください。
disable-model-invocation: true
---

# Poteto help

pstack に関するユーザーの質問に答え、送信可能なプロンプトを渡し、その回答が得られたファイルへのリンクを提供します。ヘルプ質問の場合は、作業を開始してはいけません。ユーザーは「どうやるか」を尋ねており、pstack の実行には実際にトークンを消費するので、プロンプトはユーザー自身に送らせてください。

「pstack を使ってこのバグを直して」のような作業を依頼するメッセージは、ヘルプ質問ではありません。[`poteto-mode`](../poteto-mode/SKILL.md) を読み、その配下で作業を行い、Custom Mode にすれば継続的に有効になることを一度だけ言及してください。

このファイルは、質問を回答を含むスキルやガイドページにマッピングするものです。詳細はそれらのファイルが保持しています。引用する前にリンク先のファイルを読み、このマップと矛盾する場合はリンク先を信頼してください。ここのリンクはインストールされたプラグイン内を指しており、ユーザーが開けない場合があるので、ファイルの公開コピーを渡してください: `https://github.com/cursor/plugins/blob/main/pstack/` に続けてそのパスです。

## 必要なものを見つける

メッセージと会話から必要性を推測します。「どのスキルが PR をレビューする？」のような名前付き状況の場合は、該当セクションに直行してください。必要性がまだ不明確な場合は、以下の選択肢で一度だけ複数選択の質問をし、ユーザーが選んだセクションのみ回答してください:

- セットアップする
- `/poteto-mode` でタスクを開始する
- 状況に応じたスキルを選ぶ
- うまくいかなかった実行を修正する
- pstack を自分用にカスタマイズする

回答を変える状態をチェックし、それが影響する場合のみ言及してください:

- `~/.cursor/rules/pstack-models.mdc` がない → このユーザーに対して `/setup-pstack` が実行されていないので、すべてのロールがデフォルトモデルを使用します。
- プロジェクト内に `verify-*` スキルや他のアプリハーネスがない → エージェントがアプリを操作するスクリプト化された方法を持ちません。変更が機能することを証明する質問の場合は `/create-verification-skill` を言及してください。
- プロジェクト内に形式的検証スキルがない → テストが到達する以上のプロパティはチェックされません。レース、不変条件、またはテストがほとんど到達しない他のケースについての質問の場合は `/create-formal-verification-skill` を言及してください。既に存在していて、それが最後に同期した時点として記録しているコミット以降にカバーしているコードが変更されている場合、質問がそのコードに関わるなら `/maintain-formal-verification-skill` を言及してください。

モデルルールがなく、それが重要である場合、ユーザーに今すぐ各ロールのモデルと推論予算を選びたいか尋ねてください。重要となるのは、ユーザーが新規の場合、質問がセットアップまたはコストに関する場合、または答えがどのモデルが実行されるかに依存する場合です。チャットごとに最大1回のみ尋ねてください。必要性も不明確な場合は、両方の質問を一緒に尋ねてください。2つの選択肢を提示します:

- 今すぐ: 入力する `/setup-pstack` を渡し、質問にも答えます。
- 後で: 質問に答え、`/setup-pstack` を実行するまですべてのロールがデフォルトモデルを維持することを1行追加します。

## セットアップする

1. チャットで `/add-plugin pstack` を使うか、サイドバーの Customize からインストールします。
2. [`/setup-pstack`](../setup-pstack/SKILL.md) を実行します。推論予算を尋ね、各ロールにモデルをマッピングし、ルールを書き込みます。ルールは新しいチャットに適用されます。その後、プロジェクトが既に持っていない場合、検証スキルと形式的検証スキルを生成することを一度ずつ提案します。
3. `/poteto-mode`、ゴール、合否を判定できるチェックで実際のタスクを開始します。

インストールだけではユーザーがスキルを呼び出すまで何も変わりません。ユーザーの言葉からロードされるのは `/setup-pstack` のみです。[README](../../README.md) と [ガイドページ1](../../docs/guide/01-setup.md) に詳細があります。[`references/prompting.md`](references/prompting.md) に従い、ユーザーの最初のプロンプトを一緒に言語化することを提案してください。

コストが懸念の場合は、トークンがどこに使われるか、どう節約するかを説明してください。pstack はサブエージェントとレビューパネルに追加のトークンを使います。`/setup-pstack` を再実行してより小さい予算または安価なモデルを選んでください。`auto` または `inherit-parent` に設定されたロールはチャットのモデルで実行されるので、チャットが Auto または安価なモデルで実行されるときはトークンを節約できます。短いパネルリストは、各エントリに1つずつサブエージェントを実行するので、より少ないサブエージェントで済みます。厳密さが必要な作業のために `/poteto-mode` を取っておいてください。

pstack は Cursor 用に作られていますが、Claude Code にも `/plugin marketplace add cursor/plugins` に続いて `/plugin install pstack@cursor-plugins` でインストールできます。そこでは、各コマンドに `pstack:` プレフィックスが付きます（例: `/pstack:poteto-mode`）。[Claude Code アダプタ](../../docs/harness/claude-code.md) は Cursor 名をマッピングし、Bugbot や Automations のような Cursor 専用機能を必要とするステップは1行のメモとともにスキップされます。

他のツールも Agent Skills フォーマットを使用しているため、スキルを読むことができます。ただし、`/poteto-mode`、`/how`、`/teach` などのワークフロースキルの多くは Cursor のサブエージェントを生成し、一部はロール別モデルを使用します。また、Custom Modes と `/loop` は Cursor 機能なので、それらの部分は動作しない場合があります。

## `/poteto-mode` でタスクを開始する

`/poteto-mode` はタスクをプレイブックに一致させ、プレイブックのステップを todo リストにコピーし、ステップが必要とする他のスキルを実行します。良いプロンプトはゴールと完了判定方法を述べます。スキルはリストしません。なぜなら手書きのシーケンスはプレイブックが保持するステップを落としたり順序を変えたりする傾向があるからです。言語化を手伝う前に [`references/prompting.md`](references/prompting.md) を読んでください。[ガイドページ2](../../docs/guide/02-poteto-mode.md) に例があります。

`/poteto-mode` が継続的に有効かどうかは、ユーザーの開始方法に依存します:

- `/poteto-mode` で Enter を押すと、スキルは1つのメッセージに添付されます。チャットが進むにつれて消えます。
- Mac では Option+Enter、Windows では Alt+Enter、またはスキルエントリから Use as Mode を使うと、Custom Mode になります。ユーザーがモードを終了するまで毎ターンコンテキスト内に残り、カジュアルなターンではコンテキスト外に留まります。
- Cursor のドキュメントは Agents Window と CLI に Custom Modes を記載しています。それ以外では、新しいタスクごとに `/poteto-mode` で開始してください。

これが話題に出たときは [Cursor のスキルドキュメント](https://cursor.com/docs/skills) をリンクしてください。チャットの途中で「new task」と言うと、モードは新しいプレイブックに一致します。`/poteto-mode` は既にプレイブックステップが生成するサブエージェントに `poteto-agent` を使用しています。自分のサブエージェントから同じスタイルを得るには、`subagent_type: "poteto-agent"` で生成してください。

## スキルを選ぶ

デフォルトの答えは `/poteto-mode` です。ステップが必要とするときに他のほとんどを実行します。ユーザーがプレイブックが与えるものより多いまたは少ない何かを望むときは、スキルを直接名指しします。推薦する前にスキルを読み、1つの例プロンプトを与えてください。

| ユーザーがやりたいこと | スキル |
|---|---|
| 厳密さを持って非自明なタスクをこなす | [`/poteto-mode`](../poteto-mode/SKILL.md) |
| 開始前にエージェントがゴールと問題を理解したか確認する | [`/in-your-own-words`](../in-your-own-words/SKILL.md) |
| 送信前にプロンプトを深く書き直してもらう（大きくではなく）: トッププロフェッショナルが追加するもの、彼らの生素材と手法、外部チェック。プロンプトを返してタスクは行わない | [`/sharpen`](../sharpen/SKILL.md) |
| コードが今どう動作するか、または新しいコードがどこに住むべきかを知る | [`/how`](../how/SKILL.md) |
| コードがこの形をしている理由、または数値がどこから来たかを知る | [`/why`](../why/SKILL.md) |
| 変更またはサブシステムを平易に説明された形で理解する | [`/teach`](../teach/SKILL.md) |
| トピックに関する自分の最近の作業をキャッチアップする | [`/recall`](../recall/SKILL.md) |
| ユーザーの最後のメッセージ以降このチャットで変わったことを、平易な言葉と小さな視覚、ユーザー待ちの項目をすべて列挙し返信だけで決定可能な形でキャッチアップする | [`/ketchup`](../ketchup/SKILL.md) |
| `/ketchup` と同様にキャッチアップするが、字幕付きの短いスライド動画で、待っている呼び出しはテキストでその下に表示 | [`/ketchup-video`](../ketchup-video/SKILL.md) |
| 小さな diff がそれ自体の外で何を壊しうるか知る | [`/blast-radius`](../blast-radius/SKILL.md) |
| 関数境界を跨ぐコードの前に型とモジュール形状を確定させる | [`/architect`](../architect/SKILL.md) |
| 1つのブリーフに対する複数の試みを得て、最良のものにマージする | [`/arena`](../arena/SKILL.md) |
| スライス全体で並列チェックを実行する、またはワーカーをクラウドエージェントとして競走させる | [`/swarm`](../swarm/SKILL.md) |
| 異なるモデルに diff をレビューさせ、壊そうとさせる | [`/interrogate`](../interrogate/SKILL.md) |
| 安価なローカルテストが存在するときテストファーストでバグを修正する | [`/tdd`](../tdd/SKILL.md) |
| `.ts` または `.tsx` の作業に TypeScript ルールを適用する | [`/typescript-best-practices`](../typescript-best-practices/SKILL.md) |
| コメントを書いていないレビュアーを使ってレビュー前にコメントを剥ぐ | [`/no-comments`](../no-comments/SKILL.md) |
| 散文から AI の特徴を取り除く | [`/unslop`](../unslop/SKILL.md) |
| ドキュメント、RFC、README、PR 説明、またはコミットメッセージを基準に従って書く | [`/technical-writing`](../technical-writing/SKILL.md) |
| 前の返信を平易な言葉でもう一度聞く | [`/bro`](../bro/SKILL.md) |
| エージェントにアプリを操作して動作を証明するスクリプト化された方法を与える | [`/create-verification-skill`](../create-verification-skill/SKILL.md) |
| 検証スキルとその機能マップをアプリと再び一致させる | [`/maintain-verification-skill`](../maintain-verification-skill/SKILL.md) |
| テストがほとんど到達しないレース、不変条件、または他のプロパティを形式的に検証する | [`/create-formal-verification-skill`](../create-formal-verification-skill/SKILL.md) |
| カバーしているコードが変更された後、形式的検証を再び一致させ、失敗がドリフトかバグかを知る | [`/maintain-formal-verification-skill`](../maintain-formal-verification-skill/SKILL.md) |
| PM またはデザイナーに、ユーザーが見るものを TLA+ モデルでチェックされたチャートとして、ルールを破る各状態への最短経路とともに表示する | [`/statechart`](../statechart/SKILL.md) |
| 報告または行動する前にパフォーマンス数値を吟味する | [`/benchmark-checklist`](../benchmark-checklist/SKILL.md) |
| 大規模または横断的な変更、または一旦離れた後にレビューするものを実行する | [`/figure-it-out`](../figure-it-out/SKILL.md) |
| 実行中は決定ログを保持し、後でレビューする | [`/show-me-your-work`](../show-me-your-work/SKILL.md) |
| 各ロールのモデルと推論予算を選ぶ | [`/setup-pstack`](../setup-pstack/SKILL.md) |
| 自分の作業習慣を個人モードスキルに変える | [`/automate-me`](../automate-me/SKILL.md) |
| 完了したタスクが教えたことをスキル編集に変える | [`/reflect`](../reflect/SKILL.md) |
| このリポジトリでエージェントが同じ間違いを繰り返さないようにする | [`/correct`](../correct/SKILL.md) |
| Webhook 経由で Grok Bot を起動するボタンを持つページを構築する | [`/make-bot-ui`](../make-bot-ui/SKILL.md) |
| pstack 内で方向性を見つける | `/poteto-help` |

このファイルの隣のスキルディレクトリが表にない場合、その frontmatter を読み、description でルーティングしてください。`principle-*` ディレクトリは下記の原則の下でカバーされます。

紛らわしいケース:

- `/how` はコードが何をするかを説明します。`/why` は理由を説明します。`/teach` は片方または両方を実行し、結果を平易に説明します。
- `/arena` は全ワーカーに同じブリーフを与え、最良の部分をマージします。`/swarm` はスライスまたはレースに作業を分割し、1つのレポートを返します。
- `/interrogate` は diff をレビューします。`/blast-radius` は diff の外で破壊を探し、変更を安全にする1つの事実を証明します。
- `/recall` は最近のチャット全体でコンテキストを再構築します。`/ketchup` はこのチャットのユーザーの最後のメッセージ以降のみをカバーします。1つの特定のチャットまたはブランチを再開するのは Session pickup プレイブックです。
- `/figure-it-out` は厳密な実行を設計します。Autonomous run プレイブックは1つのタスクを完了条件まで駆動します。

pstack にないもの:

- `/deslop`、`control-cli`、`control-ui` は `cursor-team-kit` プラグインで提供されます。
- `/loop` と `/create-skill` は Cursor のビルトインです。
- pstack には `/orchestrate` スキルはありません。スラッシュメニューに `/orchestrate` が表示される場合、別のプラグインが提供しています。

## プレイブックと原則

プレイブックは `/poteto-mode` 内のステップリストであり、スキルではないので、スラッシュコマンドはありません。`/poteto-mode` の内部で、タスクを記述すると1つが選ばれ、以下のフレーズは直接1つを名指しします:

- 「babysit this pr」または「check on pr 123」は Babysit を実行します。PR をマージ準備完了まで駆動し、そこで停止します。ユーザーがマージ、ランド、またはシップするよう指示しない限りマージしません。
- 「land the stack」は Shipping を実行します。
- 「take over this branch」は Session pickup を実行します。
- 「pause safely」は Pause safely を実行します。
- 「full autopilot on this queue」は Autopilot-full を実行します。「autopilot, don't merge」は Autopilot-stack を実行します。
- 「run the eval playbook」は Eval を実行します。

`/poteto-mode` なしで「babysit this pr」のようなフレーズを使うと、同じ仕事に対して Cursor 自身のスキルが代わりに起動することがあります。[`poteto-mode`](../poteto-mode/SKILL.md) の Playbooks セクションにすべてのプレイブックが列挙されています。[ガイドページ6](../../docs/guide/06-verify-and-ship.md) は PR を開く、ベビーシットする、ランドすることをカバーします。

pstack にはプランニングスキルがありません。Cursor の Plan Mode が並行して動作します。フェーズをまたぐ作業またはスタックされた PR に対して、`/poteto-mode` にプランを求めると [Multi-phase plan playbook](../poteto-mode/playbooks/multi-phase-plan.md) が実行され、プランを書きますが実装はしません。デザイン質問の場合は、Prototype プレイブックまたは `/architect` が先にコードで決着をつけます。

原則は `/poteto-mode` が読み、返信で引用する1ルールスキルです。ユーザーが直接呼び出すことはめったにありません。代わりに名前でステアリングします（例: 「apply prove it works. show me the real output.」）。`/principle-<name>` と入力すれば必要に応じてロードできます。[ガイドページ8](../../docs/guide/08-principles.md) に列挙されています。

## うまくいかなかった実行を修正する

| 症状 | 修正 |
|---|---|
| 数ターン後にモードが適用されなくなった | Enter で開始されました。Custom Mode として開始するか、各タスクを `/poteto-mode` で開始してください。 |
| 質問が前のタスクの次のステップとして扱われた | 「new task」と言うか、そのターンにモードは不要と言ってください。 |
| 新しいモデル選択が効果を持たなかった | `/setup-pstack` からのルールは新しいチャットに適用されます。新しいチャットを開始してください。 |
| 実行が予想より高コスト | セットアップの下のコスト段落を参照してください。 |
| スキルが自動でロードされなかった | ユーザーの言葉からロードされるのは `/setup-pstack` のみです。他はユーザーが入力するか、`/poteto-mode` が実行するときにロードされ、すべてのスキルは実行されません。 |
| 並列エージェントが互いに上書きした | 各エージェントに自分の worktree を与えるか、クラウドエージェントとして実行してそれぞれ自分のマシンを持たせてください。 |
| 一晩の実行が動いたが何も完了しなかった | `/loop` には合否判定できるチェックが必要で、時間ではありません。[ガイドページ7](../../docs/guide/07-overnight.md) を参照してください。 |
| 返信がグリーンビルドから成功を主張している | 実際のコマンド、フロー、保存された値、またはプロファイルを求めてください。それが prove-it-works 原則です。 |

ドリフトする実行については、[`references/prompting.md`](references/prompting.md) に1行のステアリングがあります。[ガイドページ10](../../docs/guide/10-recipes-and-pitfalls.md) にはより多くの落とし穴とコピーする価値のあるレシピがあります。

## pstack を自分用にカスタマイズする

- [`/automate-me`](../automate-me/SKILL.md) はユーザー自身の履歴から個人モードスキルをドラフトし、`/poteto-mode` と並べて使用します。
- セッション後の [`/reflect`](../reflect/SKILL.md) は、その教訓をユーザーが承認するスキル編集に変えます。
- `/poteto-mode write a skill for <workflow>` は authoring プレイブックを実行します。eval プレイブックはスキル変更をブラインドでテストします。
- 誤動作するスキルは、うまくいかなかった機能作業の中ではなく、それ自身の PR で修正してください。

[ガイドページ9](../../docs/guide/09-make-it-yours.md) がこれらをカバーします。

## 返信

答えから始めてください。コードブロックで最大1つの例プロンプトを与え、[`references/recipes.md`](references/recipes.md) から適合するものを適応させ、その後そのファイルへのリンクを置いてください。ユーザーが全マップを求めない限り短く保ってください。
