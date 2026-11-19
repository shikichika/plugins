# Search notes

Search は、ユーザーがタイトルまたは本文テキストでノートを見つけ、マッチしたノートを確認し、マッチなしと検索不可を区別できるようにする。

## Sub-features

- `search-open` は、サポートされている各ブラウザエントリポイントから検索を開く。
- `search-match` は、ノートデータを変更せずにタイトルと本文のマッチを返す。
- `search-open-result` は、結果をノートエディタで開く。
- `search-empty` は、マッチがないクエリに対して完全な空状態を表示する。
- `search-clear` はクエリを削除し、最近のノートビューを復元する。
- `search-cli` はターミナルから同じマッチングノートを返す。

## How to get to it (user POV)

- ブラウザツールバーで `Search` ボタンを選ぶ。
- フォーカスが編集可能フィールドの外にある間、ブラウザで `/` を押す。
- ターミナルで `notes search <query>` を実行する。

## Driving it with control-notes

Preconditions:

- Notes が `http://127.0.0.1:4173` で健全である。
- 使い捨てデータディレクトリに本文テキスト `Draft budget` の `Quarterly plan` が含まれる。
- `control-notes doctor` が期待される URL とデータディレクトリを報告する。

- **ツールバーエントリ。** `Search` ボタンを選ぶ。`control-notes browser click --role button --name "Search"` を実行する。`Search notes` という名前のダイアログが表示され、その検索ボックスにフォーカスがある。
- **キーボードエントリ。** ダイアログを閉じ、ページにフォーカスし、`/` を押す。`control-notes browser press --key "/"` を実行する。同じダイアログが表示され、ページはスラッシュを挿入しない。
- **タイトルマッチ。** `quarterly` を入力する。`control-notes browser fill --role searchbox --name "Search notes" --value "quarterly"` を実行する。`Search results` リストに `Quarterly plan` が含まれ、`Grocery list` は含まれない。
- **本文マッチ。** クエリを `budget` に置き換える。`control-notes browser fill --role searchbox --name "Search notes" --value "budget"` を実行する。結果 `Quarterly plan` が本文マッチの抜粋とともに表示されたままである。
- **結果を開く。** `Quarterly plan` を選ぶ。`control-notes browser click --role link --name "Quarterly plan"` を実行する。ダイアログが閉じ、エディタの見出しに `Quarterly plan` と表示される。
- **空状態。** 検索を再度開き、`volcano` を入力する。`control-notes browser fill --role searchbox --name "Search notes" --value "volcano"` を実行する。検索完了後に `No matching notes` という名前のステータスが表示される。
- **クエリをクリアする。** `Clear search` を選ぶ。`control-notes browser click --role button --name "Clear search"` を実行する。検索ボックスが空になり、`Recent notes` リージョンが結果リストを置き換える。
- **CLI マッチ。** ターミナルから検索する。`control-notes cli -- notes search "quarterly" --format json` を実行する。終了コード `0` と stdout にタイトルが `Quarterly plan` である 1 つのオブジェクトが含まれる。
- **CLI ミス。** 存在しない値を検索する。`control-notes cli -- notes search "volcano" --format json` を実行する。終了コード `0` と stdout が `[]` である。
- **証明。** 結果が入った状態を捉える。`control-notes browser snapshot --aria --path artifacts/search/results.aria.txt` と `control-notes browser screenshot --path artifacts/search/results.png` を実行する。両方のアーティファクトが Notes、クエリ、`Quarterly plan` を識別する。

## Gotchas

- エディタや検索ボックスがフォーカスを持っている間に `/` を押すと、検索を開く代わりにテキストを挿入する。
- 結果は短いデバウンスの後に更新される。固定の sleep ではなく、結果リストまたは空のステータスを待つ。
- ユーザーが `Include archived` を有効にしない限り、アーカイブされたノートは除外される。
- CLI のデフォルトは人間が読みやすい出力である。安定したアサーションには `--format json` を使う。
- 結果を開くとブラウザの状態が変わる。別のクエリを証明する前に検索を再度開く。
