# pstack

私は [poteto](https://x.com/poteto) です。社長でも CEO でもありませんが、Meta、Netflix、Cursor で何百万行ものコードを扱ってきました。また React コアチームのメンバーとして、React Compiler の構築とメンテナンスにも携わっています。

近頃、AI は雑なコード (slop code) を書きすぎだという声が高まっています。私も同感です。20 人のスロップ職人のチームのような仕事をしたくはありません。品質を伴わないスループットは私が目指すゴールではありません。速く進みたいなら、まずは深く掘り下げることです。

**pstack は私なりの答えです。** これは Cursor で高品質なコードを日々出荷するために私自身が使っているスキルそのものです。これによって cursor は本物のエンジニアリングチームに変わります。目的は LOC を最大化することではなく、むしろその逆です。pstack は、より少なく、しかしより高品質なコードを書く手助けをします。

**pstack は恐れを知らない並列化をもたらします。** 一つのエージェントに対して深く掘り下げ、良質で検証可能なコードを書くと信頼できるなら、自信を持って真に並列化することができます。`poteto-mode` で複数のエージェントを起動し、それらが厳格なエンジニアリング原則に従って作業することを信頼してください。

**cursor はあらゆる長所を享受させてくれます。** どのフロンティアモデルにも強みと弱みがあります。pstack ではどのモデルでも使えます。実際、私のスキルの多くは各モデル固有の強みを活かすマルチモデルワークフローを採用しています。

フォークしてください。改善してください。あなたのものにしてください。PR も歓迎します！

## インストール

```bash
/add-plugin pstack
```

claude code の場合:

```bash
/plugin marketplace add cursor/plugins
/plugin install pstack@cursor-plugins
```

claude code はサードパーティマーケットプレイスについては自動更新をオフにしているので、pstack はインストール時のバージョンに留まります。オンにするには `/plugin` を実行し、**marketplaces** タブを開いて `cursor-plugins` を選択し、**enable auto-update** を選びます。手動で一度だけ更新するには `claude plugin update pstack@cursor-plugins` を実行します。

claude code はプラグインのコマンドとエージェントにプレフィックスを付けるため、`/poteto-mode` は `/pstack:poteto-mode` になります。[claude code アダプタ](./docs/harness/claude-code.md) は他のすべての cursor 名を対応付けます。claude code では、`/pstack:setup-pstack` があなたのモデル選択を `~/.claude/rules/pstack-models.md` に書き込み、bugbot や automations のような cursor 専用の機能を必要とするステップは一行のメモ付きでスキップされます。

claude code は、この readme とガイドを含む pstack の生成済みコピーを、リポジトリルートの [`harness/pstack/`](https://github.com/cursor/plugins/tree/main/harness/pstack) からインストールします。pstack を変更したあとは、リポジトリルートから `node scripts/generate-pstack-harness-trees.mjs` を実行し、その結果をコミットしてください。生成済みコピーが古いままだと CI が失敗します。

## 始め方

2 ステップです:

1. [`/setup-pstack`](./skills/setup-pstack/SKILL.md) を実行し、reasoning 予算を選び、使いたいモデルを選びます。
2. 厳密さが必要なあらゆる作業で [`/poteto-mode`](./skills/poteto-mode/SKILL.md) を使います。

初めての方は [pstack ガイド](./docs/guide/README.md) をご覧ください。セットアップとプロンプトから検証、夜間実行まで、最初の本格的なタスクをひととおり案内します。詰まったり、どのスキルが合うか分からない時は [`/poteto-help`](./skills/poteto-help/SKILL.md) に尋ねてください。

それだけです。他のスキルは状況次第で、mode スキルが必要に応じてそれらを使ってくれます。デフォルトでは、コード系の委譲 (feature、refactoring、bug fix、perf、hillclimb)、prose、judgment は opus 5.5 の xhigh に送られ、xhigh を超えるロールはありません。review panel の外では、fable 5.2 は明示的に指名された場合か、同じブリーフで opus 5.5 を 2 回実行して失敗した後にのみ動作します。デフォルトの panel は fable 5.2 / opus 5.5 / gemini 4 carbon です。[`/setup-pstack`](./skills/setup-pstack/SKILL.md) でそのいずれも変更できます。

## 使い方

タスクの最初に [`/poteto-mode`](./skills/poteto-mode/SKILL.md) を使います。これはあなたのリクエストを読み、playbook 群から選択し、ステップが必要とする時に他のスキルを実行します。

タスクを入力する前に `/` メニューから `/poteto-mode` を選択してください。そうすると cursor がメッセージにそのスキルをアタッチします。

### まずは [`/poteto-mode`](./skills/poteto-mode/SKILL.md) を使えばいい

このスキルがメインのショートカットです。エージェントに厳密なエンジニアリング作業をさせたい時は常にこれを使います。22 個の playbook が付属しています:

```
/poteto-mode this pr has a subtle bug where the scroll drifts every 750ms even when idle. repro
first, then fix and verify.
```

```
/poteto-mode i'm going to bed. land the stack even if ci flakes. i want everything merged by
morning.
```

<details>
<summary>22 個の playbook</summary>

| playbook | 用途 |
|---|---|
| [investigation](./skills/poteto-mode/playbooks/investigation.md) | 読み取り専用の質問。x はどう動くのか、なぜ y はこう作られているのか、確信があるか。 |
| [bug fix](./skills/poteto-mode/playbooks/bug-fix.md) | 欠陥を再現し、根本原因を特定し、ランタイムの証拠をもって修正する。 |
| [perf](./skills/poteto-mode/playbooks/perf-issue.md) | 計測された遅さを追跡し、ベースラインに対して改善する。 |
| [hillclimb](./skills/poteto-mode/playbooks/hillclimb.md) | 目標値に対して一つの指標を持続的かつ科学的に改善。仮説をループさせ、前後の計測を行い、受理された勝ちごとに 1 コミット。 |
| [runtime forensics](./skills/poteto-mode/playbooks/runtime-forensics.md) | 計測から、稼働中の症状 (リーク、idle-cpu の空転、グリッチ) を診断する。 |
| [trace forensics](./skills/poteto-mode/playbooks/trace-forensics.md) | 取得済みのプロファイリング成果物 (cpuprofile、trace、spindump、heap snapshot) を診断する。 |
| [feature](./skills/poteto-mode/playbooks/feature.md) | 名前付きのデータ形状から構築される、新規または変更された挙動。 |
| [refactoring](./skills/poteto-mode/playbooks/refactoring.md) | 構造や形状への、挙動を保つ変更。 |
| [prototype](./skills/poteto-mode/playbooks/prototype.md) | 設計や挙動に関する判断を安価に下すため、または経験的な分岐を観察で決着させるための使い捨てのスケッチ。 |
| [visual parity](./skills/poteto-mode/playbooks/visual-parity.md) | 2 つの実装間でのピクセル単位の UI 等価性。 |
| [authoring a skill](./skills/poteto-mode/playbooks/authoring-a-skill.md) | SKILL.md の執筆または編集。 |
| [eval](./skills/poteto-mode/playbooks/eval.md) | スキルやプロンプトの変更がエージェントの挙動にどう影響するかを、ブラインドでテストする。 |
| [babysit](./skills/poteto-mode/playbooks/babysit.md) | pr またはスタックをマージ可能な状態まで進める: コンフリクト、レビュースレッド、CI。 |
| [shipping](./skills/poteto-mode/playbooks/shipping.md) | グリーンなスタックを独立に検証し、続いて検証済みの連続したランをデフォルトでは github 経由、利用可能な場合は origin 経由で下から順にランドする。 |
| [autonomous run](./skills/poteto-mode/playbooks/autonomous-run.md) | 停止することなく長期タスクを完遂まで進める。 |
| [autopilot-full](./skills/poteto-mode/playbooks/autopilot-full.md) | 独立した pr 群を、1 pr につき 1 オーナーでマージまで進める。swarm が code-ready な HEAD を検証し、パッチへの後続の変更すべてにつき新しいチェックが入る。 |
| [autopilot-stack](./skills/poteto-mode/playbooks/autopilot-stack.md) | マージをしない autopilot-full。オペレーターがレビューしてランドできるよう pr を構築・検証し、依存先の pr の上にのみ pr をスタックする。 |
| [session pickup](./skills/poteto-mode/playbooks/session-pickup.md) | 前のエージェントが進行中だった仕事を再開または引き継ぐ。 |
| [pause safely](./skills/poteto-mode/playbooks/pause-safely.md) | 進行中の仕事を後で再開できるようにクリーンに中断する。 |
| [multi-phase plan](./skills/poteto-mode/playbooks/multi-phase-plan.md) | フェーズまたはスタックされた PR をまたぐ仕事。 |
| [worktree cleanup](./skills/poteto-mode/playbooks/worktree-cleanup.md) | マージ済みまたは放棄された worktree と古い iOS シミュレータを安全ゲート付きで刈り込み、ディスクを取り戻す。 |
| [opening a pr](./skills/poteto-mode/playbooks/opening-a-pr.md) | 小さな順序付けられたコミット群から、conventional commits 形式のタイトルとブリーフィングスタイルの本文を持つ ready pr を開く。他のすべての playbook の最後に呼び出される。 |

</details>



呼び出されると、以下を行います:

1. タスクを [playbook](./skills/poteto-mode/playbooks/) に対応付け、そのステップを verbatim でコピーした todo リストの先頭に並べて開きます。
2. ステップが発火するにつれて他のスキルへルーティングします。
3. consumer と maintainer 向けにフレーミングされた、unslop な返信を書きます。

完全なルールと playbook は [`skills/poteto-mode/SKILL.md`](./skills/poteto-mode/SKILL.md) にあります。

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) をターンをまたいで有効にしておくには、`/` メニューから選び、Enter ではなく Option+Enter (mac) または Alt+Enter (windows) を押します。そうすると [custom mode](https://cursor.com/docs/skills#using-a-skill-as-a-custom-mode) になり、cursor は agents ウィンドウと CLI で提供します。モードを抜けるまで、毎ターン context に残ります。素の Enter はそれを 1 メッセージにのみアタッチします。

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) は cursor の `/loop` コマンドと非常に相性がよく動作します。厳密さを犠牲にすることなく cursor を長時間働かせることができます。

## skills

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) は、ステップがそれらを必要とする時に以下の大半を自動で動かします (`why`、`architect`、`arena`、`swarm`、`interrogate`、`unslop`、`no-comments`、`technical-writing`、`tdd`、そして principles 群)。以下の表は、直接一つを使いたい時のためのものです:

```
/how do we cancel runs? do we have an n+1 when we look up every run to cancel?
```

```
/interrogate review this pr.
```

<details>
<summary>すべてのスキル</summary>

| スキル | 使う場面 |
|---|---|
| [`/poteto-mode`](./skills/poteto-mode/SKILL.md) | 自明でないあらゆるタスクのデフォルトのエントリポイント。 |
| [`/poteto-help`](./skills/poteto-help/SKILL.md) | pstack が初めて、またはどのスキル、playbook、原則が合うか分からない時。あなたが何をしようとしているかを把握し、その部分に答え、入力するプロンプトを渡します。`/poteto-help` と打った時にのみ動作します。 |
| [`/in-your-own-words`](./skills/in-your-own-words/SKILL.md) | 作業を開始する前にエージェントにあなたのゴールと問題を言い直してほしい時。`/in-your-own-words` と打った時にのみ動作します。 |
| [`/sharpen`](./skills/sharpen/SKILL.md) | プロンプトを送る前に、大きくするのではなく深くしたい時: 一流のプロフェッショナルならあなたが頼まなかった何を行うか、彼らが出発点にする元素材、彼らの手法とツール、そして結果が正しいという外部からのチェック。書き直されたプロンプトを返し、それは実験で決着できる選択肢をプロトタイプ化するようエージェントに依頼し、プロダクトや好みの選択はあなたに委ねます。タスク自体は行いません。`/sharpen` と打った時にのみ動作します。 |
| [`/how`](./skills/how/SKILL.md) | サブシステムがどう動くかの歩き方をしたい時。 |
| [`/why`](./skills/why/SKILL.md) | 何かがなぜこう作られたかを知りたい時。コードからではなく記録から答えます: 導入時とそれ以降の各変更に関するコミットと pr のディスカッション、そしてその痕跡が薄い時は他のソースも。著者が書いたことを引用し、自身の推論は印をつけ、記録では答えられない部分は明示します。 |
| [`/recall`](./skills/recall/SKILL.md) | 仕事を始めるまたは再開するにあたって、あるトピックに関するあなたの最近のコンテキストを、あなた自身のチャット履歴と共有された記録から再構築し、簡潔な current-state ブリーフとして渡してほしい時。 |
| [`/ketchup`](./skills/ketchup/SKILL.md) | 前回のメッセージ以降、このチャットで何が変わったかを、平易な言葉と小さなビジュアルで知りたい時。あなた待ちのすべての項目が、返信が長くなったとしても、返信のみで決定可能な形で列挙されます。 |
| [`/ketchup-video`](./skills/ketchup-video/SKILL.md) | `/ketchup` のキャッチアップをスライドの短い字幕付き動画として受け取りたい時。あなた待ちのコールはその下にテキストで表示されます。 |
| [`/blast-radius`](./skills/blast-radius/SKILL.md) | 一見小さな変更があり、それが他に何を壊し得るか、安全であるというその一つの事実を実際にコードを動かすことで証明したい時。 |
| [`/architect`](./skills/architect/SKILL.md) | 関数境界をまたぐコードを書こうとしていて、まず呼び出し側の使い方、型、モジュールの形を固めたい時。 |
| [`/arena`](./skills/arena/SKILL.md) | 同じものに対して N 個の並列の試行を行い、それぞれの良い部分を取り出したい時。 |
| [`/swarm`](./skills/swarm/SKILL.md) | 異なるスライスまたはレースをまたぐ N 個の並列ワーカーを動かし、集計された一つのレポートを得たい時。 |
| [`/interrogate`](./skills/interrogate/SKILL.md) | diff があり、異なるモデルにそれを壊そうと試みさせ、実行で証明できた指摘だけを残したい時。 |
| [`/automate-me`](./skills/automate-me/SKILL.md) | あなた自身の `-mode` スキルを、あなたが実際にどう働いてきたかから起草してほしい時。 |
| [`/make-bot-ui`](./skills/make-bot-ui/SKILL.md) | ボタンが webhook 経由で Grok Bot を起こすページまたはダッシュボードを、sender-key のハンドオフと Tailscale を含めて作りたい時。 |
| [`/setup-pstack`](./skills/setup-pstack/SKILL.md) | pstack がロールごとにどのモデルを使うかを選びたい時。モデルを検出し、config ルールを書き込みます。 |
| [`/reflect`](./skills/reflect/SKILL.md) | 長いタスクがランドし、そのレシピを skill 編集として捕捉したい時。 |
| [`/correct`](./skills/correct/SKILL.md) | エージェントを同じミスで繰り返し訂正している時。履歴からミスのクラスを掘り出し、それぞれを機能する最上位のレベル (architecture、次いで types、lint、CI、続いて tests、docs は最後) で修正し、各ルールとそれを強制するものの対応表を保ちます。 |
| [`/teach`](./skills/teach/SKILL.md) | 要約だけでなく、変更やサブシステムを実際に理解したい時。how と why を動かし、ダイアグラムを一つずつ積み上げながら一つの平易な説明に織り込みます。 |
| [`/tdd`](./skills/tdd/SKILL.md) | バグを修正中で、安価なローカルテスト経路がある時。先に失敗するテストを書き、続いて修正する。 |
| [`/benchmark-checklist`](./skills/benchmark-checklist/SKILL.md) | ベンチマークを走らせたり、speedup や regression を計測した時。報告したり行動を起こす前に、その数字を精査 (limiter、tuning、errors、繰り返し実行、end-to-end relevance) します。 |
| [`/no-comments`](./skills/no-comments/SKILL.md) | レビュー前にコメントを剥ぎ取る。Comment Sicko をスポーンし、受理された指摘を修正し、主張された制約に対して encoding を提案します。 |
| [`/typescript-best-practices`](./skills/typescript-best-practices/SKILL.md) | typescript を読み書きしている時。type-system-discipline の原則を構文に根付かせます。 |
| [`/figure-it-out`](./skills/figure-it-out/SKILL.md) | 同梱の playbook が合わない時。タスクに対して厳密で監査可能な playbook を設計します。 |
| [`/show-me-your-work`](./skills/show-me-your-work/SKILL.md) | レビュー可能な意思決定の跡をつけたい時。コミット可能な tsv に決定を記録します。 |
| [`/create-verification-skill`](./skills/create-verification-skill/SKILL.md) | プロジェクトにアプリの挙動を証明するスクリプト化された方法が無い時。あらゆる言語やプラットフォーム向けに、feature map 付きのプロジェクトローカルな verify スキルを生成します。 |
| [`/maintain-verification-skill`](./skills/maintain-verification-skill/SKILL.md) | verify スキルの feature map がアプリとドリフトした時。source wave + 1 回のライブパス、最大 1 つの PR で証明された訂正を行います。 |
| [`/create-formal-verification-skill`](./skills/create-formal-verification-skill/SKILL.md) | race、retry、ステートマシン、parser、unsafe code を壊すケースにテストが届かない時。合うツールで一つのプロパティを検証し、変更のたびにそれを再チェックするプロジェクトローカルなスキルを生成します。 |
| [`/maintain-formal-verification-skill`](./skills/maintain-formal-verification-skill/SKILL.md) | 形式的検証がカバーしているコードが前回チェック以降に変わった時。モデル、ハーネス、プロパティをコードに合わせて更新し、再実行し、各失敗が検証におけるドリフトなのかコードのバグなのかを判定します。 |
| [`/statechart`](./skills/statechart/SKILL.md) | 挙動のオーナーが仕様を読むのではなくそれを見る必要がある時。tla+ モデルをチェックし、ユーザーが見るものを mermaid の statechart として描き、ルールを壊す各状態をマークし、それぞれへの最短経路を示します。 |
| [`/unslop`](./skills/unslop/SKILL.md) | 文章を整えている時。AI の tell を取り除きます。 |
| [`/bro`](./skills/bro/SKILL.md) | 直前のメッセージを専門用語抜きの平易な人間の言葉で言い直してほしい時。 |
| [`/technical-writing`](./skills/technical-writing/SKILL.md) | docs、RFC、readme、PR 説明、commit message のためのレイヤー化されたドキュメント標準 (Diátaxis + Google developer style + STE + Global English)。 |

</details>



### 例

ほとんどの場合、私はタスクの最初に [`/poteto-mode`](./skills/poteto-mode/SKILL.md) と打ち込み、playbook へのルーティングを任せます。他のスキルはステップが必要とする時に発火します。いくつかは直接呼び出します。


<details>
<summary>すべての例</summary>

```
bug fix:           /poteto-mode this pr has a subtle bug where the scroll drifts every 750ms even
                   when idle. repro first, then fix and verify.
perf:              /poteto-mode a big list takes a second or two to load even though we virtualize.
                   run a cpu trace and tell me why.
feature:           /poteto-mode build a small feature behind a feature flag. verify it really works.
prototype:         /poteto-mode build two prototypes of the markdown renderer so we can compare.
                   spawn an agent for each.
multi-phase:       /poteto-mode open source these skills as a plugin. nothing internal leaks, work
                   in a temp dir, show me the dependency graph first.
overnight run:     /poteto-mode i'm going to bed. land the stack even if ci flakes. i want
                   everything merged by morning.
babysit:           /poteto-mode check on pr 123. anything outstanding?
visual parity:     /poteto-mode the row spacing is too tall when this flag is on. the second image
                   is correct. repro and fix until it matches.
figure it out:     /poteto-mode i'm stepping away. migrate every caller from the synchronous store
                   to the new async one, keeping behavior identical. i want to trust it was done
                   right when i'm back.
how:               /how do we cancel runs? do we have an n+1 when we look up every run to cancel?
why:               /why is this feature flag not on yet?
architect:         design this instrumentation to be high signal with no false positives. /architect
                   this first.
arena:             /arena take my prompt to the arena verbatim. i want to compare their proposals
                   with yours.
swarm:             /swarm check every package under packages/ against its check.sh. one worker per
                   package. one report.
interrogate:       /interrogate review this pr.
tdd:               /tdd implement
unslop:            can we unslop and tighten the new changes?
reflect:           /reflect that took too long. capture what we learned so the next run doesn't
                   repeat it.
correct:           /correct
show-me-your-work: /show-me-your-work keep a decision trail i can review when i'm back.
automate-me:       /automate-me
help:              /poteto-help which skill should i use to review this branch?
```

</details>

## `poteto-agent` と Comment Sicko サブエージェント

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) は、それを受け取ったチャットの中で動作します。そのチャットが playbook、todo リスト、そしてすべてのサブエージェントスポーンを所有します。

pstack は、ラン中にスポーンされる委譲先のラッパーとして [`subagent_type: "poteto-agent"`](./agents/poteto-agent.md) も同梱しています。これは、作業を始める前に `poteto-mode` をインラインの principles インデックスを含めて全文読みます。`generalPurpose` で置き換えるとその読み取りがスキップされ、ドリフトします。`poteto-agent` を playbook 自体を実行するためにスポーンしないでください。

[`/poteto-mode`](./skills/poteto-mode/SKILL.md) と [`subagent_type: "poteto-agent"`](./agents/poteto-agent.md) は同じラッパー経由でルーティングされます。

pstack は、読み取り専用のコメントレビュアーである [Comment Sicko](./agents/comment-sicko.md) を `subagent_type: "comment-sicko"` として利用できる形でも同梱しています。通常は直接ではなく [`/no-comments`](./skills/no-comments/SKILL.md) 経由で呼び出してください。

## 原則

24 個の短いスキルがあり、それぞれに原則が 1 つずつあります。`poteto-mode` はそれらをインラインで索引し、タスク開始時にそのインデックスを読みます。スタンドアロンのファイルを置いているのは、他のスキルが原則を名前で参照できるようにし、またインデックスが各ルールの完全版を指せるようにするためです。

<details>
<summary>24 個のすべての原則</summary>

| 原則 | グループ | ルール |
|---|---|---|
| [laziness-protocol](./skills/principle-laziness-protocol/SKILL.md) | core | 問題を解決する削除と最小の変更をバイアスとして優先する。 |
| [foundational-thinking](./skills/principle-foundational-thinking/SKILL.md) | core | ロジックを書く前に適用する: コア型とデータ構造の選択、scaffold 対 feature 作業の順序付け、並行アクターが何を共有するかを問う。データ構造を正しく決めれば下流のコードは自明になる。 |
| [redesign-from-first-principles](./skills/principle-redesign-from-first-principles/SKILL.md) | core | 要件が初日からの基礎的前提であったかのように再設計する。後付けで取り付けない。 |
| [attack-the-premise](./skills/principle-attack-the-premise/SKILL.md) | core | 一つの前提を共有する 2 つ以上の修正が同じゲートで失敗した時に適用する。その前提を書き下し、それを前提とする次の修正を書く前に直接テストする。 |
| [subtract-before-you-add](./skills/principle-subtract-before-you-add/SKILL.md) | core | まず死んだ重み、冗長な validator、スタブ参照を取り除き、よりシンプルな土台の上に積み上げる。 |
| [minimize-reader-load](./skills/principle-minimize-reader-load/SKILL.md) | core | 質問から回答までの層の数と、読み手の頭の中にある隠れた状態を数え、呼び出し元が 1 つのラッパーを畳み、可変スコープを縮める。 |
| [outcome-oriented-execution](./skills/principle-outcome-oriented-execution/SKILL.md) | core | 明示的なフェーズ境界を持つ計画された書き換えと移行の間に適用する。目標アーキテクチャに収束させる。使い捨ての互換コードで滑らかな中間状態を保たない。 |
| [experience-first](./skills/principle-experience-first/SKILL.md) | core | 実装の利便性より user の delight を選ぶ。多くの粗い機能より少ない磨かれた機能を出す。 |
| [exhaust-the-design-space](./skills/principle-exhaust-the-design-space/SKILL.md) | core | コミットする前に 2〜3 の競合するプロトタイプを作り、横並びで比較する。 |
| [build-the-lever](./skills/principle-build-the-lever/SKILL.md) | core | 大量作業に限らず、自明でないあらゆる作業に適用する: edit、migration、analysis、check。手作業ではなく、それを行うまたは証明するツール (codemod、スクリプト、ジェネレータ、またはサブエージェントが従うスキル) を作る。そのツールはレビュアーが再実行できる成果物である。 |
| [model-the-domain](./skills/principle-model-the-domain/SKILL.md) | architecture | ドメインを散らばる条件分岐ではなく構造にエンコードする。 |
| [boundary-discipline](./skills/principle-boundary-discipline/SKILL.md) | architecture | ガードはシステム境界 (CLI、config、network、外部 API) に集中させる。内部の型を信頼し、ビジネスロジックは純粋関数に保つ。 |
| [type-system-discipline](./skills/principle-type-system-discipline/SKILL.md) | architecture | 不正な状態を表現不能にし、semantic primitive に brand を付け、外部データを境界でパースし、variant を網羅し、authoritative なスキーマから派生させる。 |
| [make-operations-idempotent](./skills/principle-make-operations-idempotent/SKILL.md) | architecture | 以前の部分実行の有無にかかわらず、同じ最終状態に収束する。 |
| [migrate-callers-then-delete-legacy-apis](./skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) | architecture | 互換レイヤーを残すのではなく、同じ波の中で caller を移行し古い API を削除する。 |
| [separate-before-serializing-shared-state](./skills/principle-separate-before-serializing-shared-state/SKILL.md) | architecture | まず共有をなくす。共有する writer が 1 つであることが本当の不変条件である場合にのみ、構造的にシリアライズする。 |
| [prove-it-works](./skills/principle-prove-it-works/SKILL.md) | verification | タスク完了後、完了宣言の前に適用する。実際のアーティファクトに対して検証する (機能を実行し、実際の値を読み、diff を確認する) 。プロキシ、自己報告、"コンパイルが通る" ではなく。 |
| [fix-root-causes](./skills/principle-fix-root-causes/SKILL.md) | verification | 各症状をシステムが最初に誤った場所まで遡り、そこで直す。ガードが扱う状態が正当である時にのみ、ガードは修正である。 |
| [sequence-verifiable-units](./skills/principle-sequence-verifiable-units/SKILL.md) | verification | 多段の作業 (sweep、migration、類似した edit の連続) 、およびコミットと PR のスタックの仕方に適用する。それぞれが検証可能な状態で終わる小さな単位に作業を分け、次に進む前に各々を確認し、連なり自体がレビュアーに自身を証明するように配信を並べる。 |
| [test-behavior-not-implementation](./skills/principle-test-behavior-not-implementation/SKILL.md) | verification | テストを書く、変える、保つ時に適用する。コードの利用者が観測できるものをアサートする。そうすることで、その挙動が壊れた時にテストが失敗し、内部だけが変わった時には通り続ける。 |
| [explain-the-number](./skills/principle-explain-the-number/SKILL.md) | verification | 計測した数字 (speedup、regression、throughput、latency、eval の結果) を信じ、報告し、行動に移す前に適用する。何がそれを制約しているかを見つけ、それが想定した仕事以外の何かを計測していないことを排除する。 |
| [guard-the-context-window](./skills/principle-guard-the-context-window/SKILL.md) | delegation | 大量の仕事はサブエージェントにルーティングし、メインスレッドには要約を残す。raw のペイロードは残さない。 |
| [never-block-on-the-human](./skills/principle-never-block-on-the-human/SKILL.md) | delegation | 進めて結果を提示し、人間には事後にコースを修正させる。確認は不可逆な行動のためにのみ取っておく。 |
| [encode-lessons-in-structure](./skills/principle-encode-lessons-in-structure/SKILL.md) | meta | ルールはテキストを増やすのではなく、lint、メタデータフラグ、ランタイムチェック、スクリプトとしてエンコードする。 |

</details>

## ここに同梱されていないもの

`poteto-mode` が参照するが同梱していないものがいくつかあります:

- `/deslop` と `deslop` スキルは `cursor-team-kit` プラグインに同梱されています。
- `control-cli` (CLI と TUI 向け) と `control-ui` (ブラウザ、Electron、web 向け) も `cursor-team-kit` に同梱されています。
- `/create-skill` は cursor の組み込みです。cursor は pr を監視する組み込みの `/autopilot` も提供します。`poteto-mode` の中では、pr-status のリクエストは代わりに [babysit playbook](./skills/poteto-mode/playbooks/babysit.md) に送られます。

フルセットが欲しい場合は、pstack と並行して `cursor-team-kit` もインストールしてください。

## なぜ planning スキルが無いのか？

cursor にはすでに素晴らしい plan mode があり、pstack とも非常によく動作します。しかし個人的には、私は planning を信じていません。最良のスペックはコードです。それでも計画を立てたい場合は、[`/poteto-mode`](./skills/poteto-mode/SKILL.md) がそれをカバーしますが、デフォルトではありません。

## あなたのものにする

`poteto-mode` は私のスタイルです。それがあなたの望むものと一致するとは限りません。

[`/automate-me`](./skills/automate-me/SKILL.md) と入力してください。これは最近のトランスクリプトを掘り起こし、あなたが実際にどう働いてきたかから `<your-name>-mode` スキルを起草し、下では pstack 経由でルーティングします。pstack をベースとして保ちつつ、`poteto-mode` と並んで自分自身のルーティングスキルを得ることになります。

モデルも設定可能です。[`/setup-pstack`](./skills/setup-pstack/SKILL.md) と入力してください。アクセスできるモデルを検出し、各ロール (code、judgment、review panels) をモデルに対応付ける小さな常時適用ルールを書き込みます。すべてのスキルがそれを読み、ルールが無い時は妥当なデフォルトにフォールバックするので、オーバーライドしたいものだけをオーバーライドします。

デフォルトが変わった時、変更前に書かれたルールは、再度 `/setup-pstack` を実行するまで古いデフォルトを pin し続けます。再実行は、認識できる古いデフォルトを置き換え、モデルがデフォルトと異なるその他のロールは保ちます。認識できない pin を外すには、まずそのロールの行またはファイルを削除してください。

## automations

pstack はまた、休眠状態の [benny automation pack](./automations/benny/) を同梱しています。benny は slack の issue レポートをトリアージし、確認されたバグを実際の UI 証拠とともに再現・修正します。これらのファイルは slash スキルとして登録されていません。

セットアップするには、cursor を [`FOR_AGENTS.md`](./automations/benny/FOR_AGENTS.md) に向けてください。セットアップは pack を対象リポジトリの `.cursor/automations/benny/` にコピーし、そこで共有スキルのために pstack を有効化し、ユーザー設定はコピーされた pack の外に保ちます。benny は cursor automations 上で動作するので、cursor 専用です。

## ライセンス

MIT
