# Claude Code での pstack

pstack スキルは Cursor の名前を使っています。Claude Code では、セッションに 1 度このファイルを読み、下の表でスキル内の各 Cursor 名をマップしてください。`<pstack>` は `skills/` と `docs/` を保持するフォルダを指します。Cursor のフィールドに対応する行がない場合、それを落としてそう言ってください。ツールスキーマが拒否するフィールドや値を決して渡さないでください。

## ツール

| Cursor | Claude Code |
| --- | --- |
| `Task` | `Agent` |
| `AskQuestion` | `AskUserQuestion`。`-p` モードでは存在しないので、プレーンテキストで尋ねる。 |
| todolist を開く、またはステップを todolist ツールにコピーする | セッションが `TodoWrite` を提供していればそれを使う。そうでなければ、返信内でリストを保持し、フェーズごとに更新する。 |
| 別の pstack スキルを実行する（`/name` または「**name** スキル」） | `Read` で `<pstack>/skills/<name>/SKILL.md` を開き、それに従う。`Skill` ツールはほとんどの pstack スキルを拒否する。ユーザーに入力するよう伝えるときは `/pstack:<name>` と書く。 |
| `Grep`、`Glob` | セッションに `Grep` も `Glob` もなければ、`Bash` で `rg` を使う。 |

## サブエージェントのフィールド

| Cursor | Claude Code |
| --- | --- |
| `subagent_type: generalPurpose` | `subagent_type: "general-purpose"` |
| `poteto-agent`、`comment-sicko` | `pstack:poteto-agent`、`pstack:comment-sicko` |
| `poteto-agent` のファイルにある `model:` 行 | Cursor スラッグを保持している。`pstack:poteto-agent` を spawn する各 `Agent` 呼び出しで、Models 表を使ってマップし、`model` と `effort` を渡す。そのスラッグが API に届かないようにする。 |
| `readonly: true` | `subagent_type: "Explore"`。`Bash` を保持するので、ブリーフにも「ファイルを編集しない」と書き、終了後に `git status` をチェックする。 |
| `readonly: false` | `general-purpose`。セッションの MCP サーバを保持する。 |
| `environment: "cloud"` | `isolation: "worktree"`。ワーカーがチェックアウトするブランチまたはコミットを命名する。 |
| `environment: "local"` | 省略。 |
| `cloud_base_branch: <b>` | ワーカーに最初に `<b>` をチェックアウトするよう伝える。 |
| `run_in_background: true` | 省略。サブエージェントはデフォルトでバックグラウンドで動き、完了時に通知する。 |
| `resume` | 新しいエージェントを spawn し、以前の結果をブリーフにペーストする。 |
| `file_attachments` | パスをブリーフに列挙する。 |

## モデル

| Cursor | Claude Code |
| --- | --- |
| `claude-opus-5-5-xhigh` のような `claude-<family>-<version>-<effort>[-fast]` | `model: "opus"` と `effort: "xhigh"`。`model` は `opus`、`sonnet`、`haiku`、`fable` のみを受ける。`-fast` は落とす。 |
| `gemini-*` または他の非 Claude スラッグ | `model` を省略してエージェントがセッションモデルで動くようにし、そう言う。 |
| `auto` または `inherit-parent` | `model` を省略。 |
| `pstack-models.mdc` ルール、`~/.cursor/rules/pstack-models.mdc` | `~/.claude/rules/pstack-models.md`。Claude Code はこれをすべての新しいセッションに読み込むので、そのロール行はすでにコンテキストにある。ない場合はファイルを読む。ファイルやロールの行がない場合は、スキルのデフォルトスラッグを使う。 |
| `opus high` のような `<model> <effort>` のロール値 | `model: "opus"` と `effort: "high"`。 |

## setup-pstack

Claude Code は新しいセッションごとに `~/.claude/rules/` 内の各 `.md` ファイルをロードするので、`setup-pstack` はルールをそこに書き込みます。次の変更でステップに従ってください。

- Detect。検出されるモデルは `Agent` ツールスキーマが列挙する `model` 値であり、それぞれ `effort` 値から 1 つの effort を伴う。実在のモデルは `opus xhigh` のように `<model> <effort>` と書く。effort は最後のトークンなので、budget のステップはそのまま機能する。
- Defaults。各デフォルトスラッグを上の行でマップする。`claude-opus-5-5-xhigh-fast` は `opus xhigh`、`claude-opus-5-5-high-fast` は `opus high`、`claude-fable-5-2-high` は `fable high`、`gemini-4-carbon-high` は `inherit-parent`。
- Write。frontmatter なしで `~/.claude/rules/pstack-models.md` を書き、次にコメント行、`# budget` 行、ステップ 5 のロールごとに 1 行を書く。ファイル全体を上書きする。ロール行を `CLAUDE.md` や `AGENTS.md` に置かない。

## パス

| Cursor | Claude Code |
| --- | --- |
| `.cursor/skills/`、`~/.cursor/skills/` | `.claude/skills/`、`~/.claude/skills/` |
| `~/.cursor/plugins/`、`.cursor-plugin/plugin.json` | `~/.claude/plugins/`、`.claude-plugin/plugin.json` |
| `.cursor/rules/*.mdc`、always-applied ルール | `CLAUDE.md`、または `CLAUDE.md` がない場合は `AGENTS.md`。Claude Code は `.mdc` ファイルを決して読まない。モデルルールは例外。Models を参照。 |
| `https://github.com/cursor/plugins/blob/main/pstack/<path>` | そのファイルが存在する場合は `<pstack>/<path>`。プラグインはスキル、`docs/guide/`、`README.md` を同梱している。ローカルコピーを読み、決して fetch しない。ユーザーにリンクが必要なときは URL を渡す。 |
| `~/.cursor/projects/<slug>/agent-transcripts/` 内のトランスクリプト | `~/.claude/projects/<slug>/<session>.jsonl`。スラッグはワークスペースパスで、各 `/` と `.` を `-` に変えたもの。だから `/Users/you/proj` は `-Users-you-proj` になる。そのフォルダ直下の最新の `.jsonl` が現在のセッション。その `<session>/subagents/` フォルダがサブエージェントのトランスクリプトを保持する。このワークスペースのフォルダのみを読む。 |

## Cursor 専用機能

Claude Code には Cursor Automations、Bugbot、Cursor クラウドエージェント、組み込み PR ツールがない。ステップがそれらを必要とする場合、`Skipped in Claude Code: <step>. <reason>.` という 1 行を返信に入れ、次のステップに進む。停止、質問、リトライはしない。後のステップがスキップされたステップが作ったもの（URL やキーなど）を必要とする場合、プレースホルダを残して行で命名する。

| Cursor | Claude Code |
| --- | --- |
| Cursor Automations: routine、`update_state` routine 呼び出し、`api2.cursor.sh/automations` webhook、routine wake、`SendToUser` secret 要求 | 行とともにスキップ。 |
| Bugbot | GitHub PR 上の Bugbot コメントは普通のレビュースレッドなので、ステップが与える forge コマンドでトリアージする。PR に Bugbot チェックやコメントがない場合、Bugbot を開始、待機、または設定するステップはスキップする。 |
| Cursor クラウドエージェント | `environment: "cloud"` は、サブエージェントのフィールドで示したとおり worktree にマップする。クラウドエージェント自身のダッシュボードステータスやそれ自身のマシンのような、クラウドエージェント自身を必要とするステップはスキップする。 |
| 組み込み PR ツール | `gh pr create` のような、ステップが与える forge コマンドを使う。それはスキップではないので、行を書かない。 |
