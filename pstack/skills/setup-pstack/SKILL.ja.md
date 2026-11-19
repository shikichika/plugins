---
name: setup-pstack
description: pstack がロールごとに使用するモデルとその推論予算を設定する。利用可能なモデルを検出し、スキルのデフォルトを上書きする常時適用ルールを書く。/setup-pstack、"configure pstack models"、"pstack budget"、または pstack のモデル選択の変更に使用。
---

# Setup pstack

ロールごとに pstack のモデルを設定する常時適用ルールである `~/.cursor/rules/pstack-models.mdc` を書く。

## ステップ

### 1. 利用可能なモデルを検出する

このセッションで `Task` サブエージェントに渡せるモデルスラッグを列挙する。それが信頼できるソースだ。Cursor がユーザーのエンタイトルメントされたモデルをリストするモデル API や CLI も公開している場合、完全性のためにそれを優先する。何も検出できない場合、ユーザーにアクセス権のあるスラッグを貼り付けるよう頼む。利用可能であることを確認していない実際のスラッグを決して書かない。エイリアス `inherit-parent` と `auto` は、検出されたスラッグではないが、常に有効である。

### 2. 現在の状態をロードする

デフォルトのロールからモデルへのマッピングは、下のステップ 5 に示すルール形状である。`~/.cursor/rules/pstack-models.mdc` が既に存在する場合、それを読み、その `# budget` 行とそのロール値を現在の選択として扱う。それ以外はそれらのデフォルトから始める。ロールがステップ 5 にない行（`hardest tasks`、`architect runners`、`reflect tooling`、`why investigators` など）は、廃止されたロールからのものである。削除する。

`# defaults: 3` 行のないルールは、より古いセットアップからのものである。そのセットアップはすべてのロールに対して 1 行を書いたので、その値の一部は選択ではなく古いデフォルトである。ステップ 3 の前に、これらの値を置き換える:

- 正確に 1 つの `claude-fable-*` エントリ、1 つの `claude-opus-*` エントリ、および 1 つの `gemini-*` エントリを任意の順序で含む `arena cross-judge pool` リストは、ロールのステップ 5 リストを取る。Fable と Opus は 1 つのベンダーなので、Fable エントリは Opus エントリも判断できる場所でのみ判断できる。
- `# budget: unlimited` 行は `large` になる。なぜならどのロールも `xhigh` を超えて実行されないからだ。
- ルールに `# defaults: 2` 行もない場合、古いスラッグは `grok-*` にマッチするものである。`feature, refactoring`、`bug-fix`、`perf-issue`、`hillclimb`、または `swarm workers` の古いスラッグは、ロールのステップ 5 デフォルトを取る。パネルロール（`arena runners`、`arena cross-judge pool`、`interrogate reviewers`）では、正確に 1 つの `claude-opus-*` エントリと 1 つの古いスラッグのリスト（どちらの順序でも）は、ロールのステップ 5 リストを取る。他のリストでは、各古いスラッグは `claude-opus-5-5-high-fast` になる。

他のすべての値はユーザーの選択であり、他のロールの古いスラッグ、およびすべての `inherit-parent` または `auto` 値を含めて、そのまま残る。どの値を置き換えたかをユーザーに伝える。

### 3. 予算、マップ、確認

**(a) 予算を尋ねる。** フリーテキストより AskQuestion を優先する。以下の正確なラベルを持つ 3 つのオプションを提供し、ルールが記録する場合は現在の予算を名付ける。ルールがない場合、`large` がスキルのデフォルトに一致すると言う。

- `large — xhigh reasoning`
- `medium — high reasoning`
- `small — medium reasoning`

**(b) 適用する。** スキルのデフォルトから作業テーブルを構築し、再実行時には、ファミリー、リスト、またはエイリアス（`inherit-parent`、`auto`）で変更したロールを保持する。ファミリーは **arena** スキルのフェーズ A が定義するものであり、Fable は独自のファミリーである。各ロールにはターゲット努力があり、それはデフォルトの努力が予算で移動したものである。`large` はそれを保持し、`medium` は 1 ステップ下げ、`small` は 2 ステップ下げる。ラダーは `xhigh` > `high` > `medium` > `low` である。どのロールも `xhigh` を超えて実行されず、ラダーの下にあるターゲットはそこに留まる。ほとんどのデフォルトは `xhigh` にあり、各予算ラベルはそれらが着地する場所を名付ける。パネルエントリを含むロール内のすべての実際のスラッグの努力トークンを、ロールのターゲットに設定する。努力トークンは最後のトークン、または末尾の `fast` の前のトークンである。結果が検出されたスラッグでない場合、ターゲット以下で最も高い努力を持つ同じファミリーの検出スラッグを使用し、それ以外はロールを選択が必要なものとしてマークする。`inherit-parent` と `auto` は変わらない。したがって `medium` は `claude-opus-5-5-xhigh-fast` を `claude-opus-5-5-high-fast` に、`claude-opus-5-5-high-fast` を `claude-opus-5-5-medium-fast` に変える。`small` はそれらを `claude-opus-5-5-medium-fast` と `claude-opus-5-5-low-fast` に変える。Opus 5.5 は常に `-fast` スラッグを実行するので、すべての Opus 5.5 値は `-fast` で終わる。Fable 5.2 と Gemini には fast スラッグがないので、決して書かない。Gemini は `high` が上限なので、Gemini エントリはロールのターゲットが `xhigh` のときに `high` で実行される。

**(c) ロールを表示して確認する。** すべてのロールをそのモデルと共に表示し、検出セットにない実際のスラッグを選択が必要としてマークする。また、ステップ 2 が削除した各行をリストする。そのまま受け入れるか、特定のロールを変更するか尋ね、検出されたモデルを `xhigh` 以下で、プラス `inherit-parent` と `auto`（どちらも意味する: このロールは親チャットモデルで実行される。これが Auto ユーザーが Auto に留まる方法である）をオプションとして提供する。フリーテキストより AskQuestion を優先する。パネルロール（arena runners、interrogate reviewers）では値はリストであり、1 つのサブエージェントがエイリアスエントリを含むエントリごとに実行されるので、リストの長さがカウントを設定する。`arena cross-judge pool` もリストだが、Arena は 1 つのジャッジを実行する。親と異なるベンダーからの値のみを、リストの順序で試すので、優先順で順序付ける。Fable と他の Claude モデルは 1 つのベンダーである。`inherit-parent` または `auto` 値は決して判断しない。`swarm workers` は、レースや比較がアームごとに別のモデルを割り当てない限り、すべてのワーカーのデフォルトモデルである。

### 4. 検証

書かれるすべての実際のスラッグは、検出セット内で、`xhigh` 以下でなければならない。`inherit-parent` と `auto` は常にパスする。選択された実際のスラッグが利用できない場合、停止して再度尋ねる。

### 5. ルールを書く

`~/.cursor/rules/pstack-models.mdc` を `alwaysApply: true`、`# defaults: 3` 行、選択されたラベルとそのラベルが名付ける努力を含む `# budget` 行、およびロールごとに 1 行で、poteto-mode が使用するのと同じラベルで書く。ファイル全体を上書きして、再実行がべき等であるようにする。形状:

```
---
description: pstack per-role model choices (overrides skill defaults)
alwaysApply: true
---
# pstack model configuration. One line per role. Delete a line to fall back to the skill default.
# `inherit-parent` or `auto` as a value: the role runs on the parent chat model (omit Task `model` and spawn `generalPurpose`). Alias entries in a panel list still count toward its fan-out.
# defaults: 3
# budget: large (xhigh)
feature, refactoring: claude-opus-5-5-xhigh-fast
bug-fix: claude-opus-5-5-xhigh-fast
perf-issue: claude-opus-5-5-xhigh-fast
hillclimb: claude-opus-5-5-xhigh-fast
judgment and prose: claude-opus-5-5-xhigh-fast
arena runners: claude-fable-5-2-high, claude-opus-5-5-high-fast, gemini-4-carbon-high
arena cross-judge pool: claude-opus-5-5-high-fast, gemini-4-carbon-high
swarm workers: claude-opus-5-5-high-fast
interrogate reviewers: claude-fable-5-2-high, claude-opus-5-5-high-fast, gemini-4-carbon-high
```

### 6. 確認

ユーザーに、ルールが書かれ、新しいセッションに適用されることを伝える。このスキルを再実行するとそれが更新される。

### 7. 検証スキルを提供する（オプション）

プロジェクトに証明のために実際のアプリを駆動する方法（`verify-*` スキル、または既存のハーネス）があるか確認する。ない場合、一度提供する: 「エージェントがユーザーのようにアプリを駆動し、変更が動作することを証明できるよう、プロジェクトローカルの検証スキルが欲しいですか？ /create-verification-skill で生成できます」。はいの場合、`/create-verification-skill` を呼び出す（pstack がインストールされている場所に解決される: ワークスペース、ユーザー、またはプラグイン）。いいえの場合、押し付けずに進む。

### 8. 形式検証スキルを提供する（オプション）

プロジェクトに既に形式検証スキル（モデルチェッカー、証明、または別の検証器をコードに対して実行するプロジェクトスキル）があるか確認する。ない場合、一度提供する: 「エージェントがスコープ内のすべてのケース（テストが到達するケースだけでなく）であなたのコードの主要な性質が成立することを証明できるよう、形式検証スキルが欲しいですか？ /create-formal-verification-skill で生成できます」。はいの場合、`/create-formal-verification-skill` を呼び出す（pstack がインストールされている場所に解決される: ワークスペース、ユーザー、またはプラグイン）。いいえの場合、押し付けずに進む。
