# レシピと落とし穴

コピーする価値のあるプロンプトと、誰もが一度はやるミスです。パスと完了条件はご自身のものに差し替えてください。レシピは意図的にくだけた形にしています。実際に打ち込まれるのはそういう形で、スキルは簡潔なプロンプトからも意図をうまく読み取ります。

![彼女はカウンターの上に /how、/tdd、/loop のピン留めカードが並ぶ中、ロボットがレシピ箱から料理をしているところで完成した料理を味見している。](./images/recipes.jpg)

## 不慣れなサブシステムを理解する

```text
use /how first to understand how this initialization works. then use /why to figure out why it broke recently.
```

まず仕組み、次に歴史。各スキルのレポートは検索したソースを教えるので、答えが何に根拠付けられているかが分かります。

## ノイズの多い報告をコードに触れる前に言い直す

```text
/poteto-mode read this thread. restate the underlying issue in your own words, in plain english. don't change any code yet.
```

誤読は言い直しに現れ、そこで直すのはメッセージ 1 つで済みます。エージェントが自分の見解を述べるまで、自分の仮説は秘めておいてください。`/in-your-own-words` はプレイブックなしで言い直しだけを求めます。

## プロンプトを送る前に研ぐ

```text
/sharpen write the onboarding doc for the new billing service.
```

エージェントは、エキスパートがあなたが頼まなかった何をするか、彼らが出発点とする生素材、その手法と道具、結果が正しいことを示す外部チェックを命名します。あなたのプロンプトをそれを要求する形に書き直して返し、タスクは行いません。読み直してみて良ければ送ってください。

## 選ぶ前にプロトタイプする

```text
/poteto-mode prototype a few options for the settings layout. put them behind a switcher and send me screenshots of each.
```

説明ではなく、動くものから選びます。エージェントは途中で自分のレイアウトとタイミングの問いに答えます。

## 決まった設計を計画に変える

```text
/poteto-mode turn this design into a plan. small verifiable PRs, each with its own proof.
```

設計が固まった後にのみ尋ねてください。計画が成果物であり、それを実行するプレイブックが命名されます。

## 設計へのセカンドオピニオン

```text
ask /arena for a second opinion on this thread and our approach
```

あなたの現在の設計は候補の 1 つになり、統合は、パネルがより良いものを見つけたか、あなたのものを確認したかを教えてくれます。逆転にコストがかかる設計の隣では、わずかなコストです。

## 独立したスライスを並列にチェックする

```text
/swarm check every package under packages/ against its check.sh. one worker per package. one report.
```

各ワーカーは 1 つのパッケージを所有します。親は各スライスを待ち、ワーカーの生ダンプではなく 1 つの `PASS`、`ISSUES`、`BLOCKED` レポートを返します。

## ブランチを懐疑的にレビューする

```text
/interrogate the whole branch, but skeptically. don't change anything yet. no nitpicks unless it's an actual bug or regression in behavior.
```

修飾語は本物の仕事をします。「まだ何も変えないで」は読み取り専用に保ち、nitpick ルールは事前にノイズをフィルタするので、検証済みの発見はあなたの時間の価値があります。

## 失敗するテストを通してバグを修正する

```text
/poteto-mode repro the duplicate write first. if there's a cheap test path, /tdd it. then fix and rerun.
```

「if there's a cheap test path」は重要です。脆いモックを通してテストを強制するより、実コマンドを走らせる方が証明として強く、プレイブックはそう言うことが許されています。

## 証拠付きで報告を再現し修正する

```text
/poteto-mode repro this with /verify-<app>. if it repros on main, fix it and show me a video as proof.
```

「if it repros on main」は、バグがすでに消えているときに実行を早期に止めさせます。動画は、差分を読む前に修正をチェックさせてくれます。

## テストが届きにくい性質を証明する

```text
/create-formal-verification-skill the retry queue. a message must never be delivered twice.
```

述べられた範囲内の全ケースでチェックされる 1 つの性質、コードがすでに破っているならあなたのコードの用語での反例、そしてそのコードへの変更ごとに次のエージェントが実行するプロジェクトスキルを得ます。

## 投稿前に数値を検査する

```text
/benchmark-checklist vet this 40% speedup before it goes in the pr description
```

より速い、より遅い、有意差なし、不確定のいずれかを、実行回数、範囲、数値を制限するものとともに得ます。

## 同じミスを訂正し続けない

```text
/correct agents keep adding new config flags without registering them in the schema
```

修正はアーキテクチャ、型、lint、またはテストとしてリポジトリにランディングするので、次のエージェントはそのミスができません。

## 作業を開始せずに方法を尋ねる

```text
/poteto-help how do i get poteto-mode to stay on every turn?
```

答え、送るべきプロンプト、ソースへのリンクを得ます。あなたがそのプロンプトを送るまで何も動きません。

## 離れている間、実行を正直に保つ

```text
im going to bed, keep going autonomously until every fixture passes. do not stop. keep a decision log i can audit in the morning.
```

完全な契約は [オーバーナイトのページ](./07-overnight.md) にあります。タスクと完了条件がすでに会話にあるときは、短縮形が効きます。

## 漂流する実行を軌道修正する

舵取りのプロンプトは 1 行です。

```text
i said the goal is to repro. i did not ask for a fix yet.
```

```text
apply prove it works. show me the real output, not the build log.
```

```text
/unslop that, no emdashes
```

多くの言葉はめったに要りません。正しい名前が要り、[原則のページ](./08-principles.md) がその語彙です。

## 返信を平易な言葉で得る

```text
/bro
```

それが全プロンプトです。[`/bro`](../../skills/bro/SKILL.md) は、ある人がもう一人に話すように、ジャーゴンなしで、短く、直前のメッセージを言い直します。返信が技術的に徹底しているのに何を言ったか分からないときに使ってください。

## 放置していたチャットにキャッチアップする

```text
/ketchup
```

[`/ketchup`](../../skills/ketchup/SKILL.md) は、前回のあなたのメッセージ以降に何が変わったかを平易な言葉で伝え、その後あなた待ちの各案件を推奨、証拠、リスクとともに列挙します。報告中は何も変えません。

キャッチアップを見たい場合は [`/ketchup-video`](../../skills/ketchup-video/SKILL.md) を実行してください。何が起きたかを字幕付きの短いスライド動画で示し、あなた待ちの案件をその下にテキストで列挙します。

## 落とし穴

- **プロンプトでスキルを列挙する。** 「use /architect then /arena then /interrogate」はプレイブックがすでにシーケンスしているステップを並べ替えます。ゴールと制約を述べてください。デフォルトを上書きするときだけスキルを命名してください。
- **曖昧な完了条件。** 「make it better」は `/loop` にチェックすべきものを与えません。合否判定できるコマンドかアーティファクトを与えてください。
- **原因に関する自分の仮説を先に出す。** エージェントはあなたが指差した方向を探します。問題を言い直すよう求めてから、勘を共有してください。
- **最初の設計を採用する。** 1 回の試みはモデルが最初に思いついた形で固めます。プロトタイプまたは `/architect` を求め、証拠から選んでください。
- **抽象的な計画を磨く。** コードの背後にない計画の敵対的レビューは、起こらないリスクを発明します。プロトタイプで未解決の問いを解決し、できたものをレビューしてください。
- **1 つのワークツリー内の並列エージェント。** 互いを上書きし、誰が何を変えたか分かりません。クラウドエージェントとして走らせるか、「試行ごとに独自のワークツリー」と言ってください。
- **信頼する前にループする。** 自分の作業を検証できないループは、未チェックの作業を速くするだけです。先に検証スキルを動かしてください。
- **検査されていない数値を信頼する。** 温まったキャッチやスキップされたコードパスは高速化を偽装できます。数値がどこへ行く前にも `/benchmark-checklist` を実行してください。
- **同じミスを手で訂正する。** チャットでの訂正は 1 回の実行を助けます。`/correct` は後の実行が繰り返さないようリポジトリを修正します。
- **カバレッジに `/arena` を使う。** `/arena` は 1 つの設計やコードブリーフを繰り返し、ベースを選んで最良部分を接ぎ木します。`/swarm` はスライスまたは宣言済み race arm を分割し、1 つのレポートを集約します。
- **すべてのレビューコメントを受け入れる。** ボットと人間はどちらも本物のキャッチとノイズを 1 つのリストに提出します。`/interrogate` は再実行で証明される発見だけを保ち、何を落としどうしてかを列挙します。あなたはどちらの方向にも上書きできます。
- **`auto` をモデルスラッグとして扱う。** `auto` と `inherit-parent` は「`model` フィールドを省略してサブエージェントが親チャットモデルを継承するようにする」を意味します。ロールは [セットアップ](./01-setup.md) が扱います。
- **ビルドのグリーンで成功を報告する。** ビルドはコンパイルが通ることを証明します。実コマンド、フロー、保存値、プロファイルを求め、返信で証拠を期待してください。
- **`SKILL.md` をフリーハンドで書く。** 検証とレビューが行われるよう [Authoring or modifying a skill プレイブック](../../skills/poteto-mode/playbooks/authoring-a-skill.md) 経由にルーティングしてください。

これでガイドは以上です。飛ばしてきたなら、[セットアップ](./01-setup.md) に戻って 1 つの本物のタスクを実行してください。習慣は読書ではなく使用から定着します。

[ガイドのインデックス](./README.md) に戻る。
