# Create a note

Create note は、ユーザーがタイトル付きノートをブラウザまたは CLI から保存し、未完成の下書きをキャンセルし、保存されたノートをユーザー向けの 2 つ目のビューから確認できるようにする。

## Sub-features

- `create-open` は、各ブラウザエントリポイントから空のエディタを開く。
- `create-save` はタイトルと本文を永続化する。
- `create-cancel` は未完成のブラウザ下書きを破棄する。
- `create-cli` はターミナルから同じノート形状を作成する。

## How to get to it (user POV)

- ブラウザツールバーで `New note` ボタンを選ぶ。
- フォーカスが編集可能フィールドの外にある間、ブラウザで `n` を押す。
- ターミナルで `notes create --title <title> --body <body>` を実行する。

## Driving it with control-notes

Preconditions:

- Notes が `http://127.0.0.1:4173` で健全である。
- `Release checklist` というタイトルのノートはない。
- `control-notes doctor` が期待される URL と使い捨てデータディレクトリを報告する。

- **エディタを開く。** `New note` を選ぶ。`control-notes browser click --role button --name "New note"` を実行する。`Note editor` という名前のフォームが表示され、`Title` テキストボックスにフォーカスがある。
- **コンテンツを入力する。** タイトルと本文を入力する。`control-notes browser fill --role textbox --name "Title" --value "Release checklist"` と `control-notes browser fill --role textbox --name "Body" --value "Tag and publish"` を実行する。`Save note` ボタンが有効になる。
- **ノートを保存する。** `Save note` を選ぶ。`control-notes browser click --role button --name "Save note"` を実行する。`Note saved` という名前のステータスが表示され、見出しに `Release checklist` と表示される。
- **永続化を確認する。** ノートリストに戻り、ノートを再度開く。`control-notes browser click --role link --name "All notes"` と `control-notes browser click --role link --name "Release checklist"` を実行する。エディタが両方の保存された値を表示する。
- **下書きをキャンセルする。** 新しいノートを開き、`Discard me` を入力し、`Cancel` を選ぶ。`control-notes browser click --role button --name "New note"`、`control-notes browser fill --role textbox --name "Title" --value "Discard me"`、`control-notes browser click --role button --name "Cancel"` を実行する。ノートリストが戻り、`Discard me` のリンクはない。
- **CLI エントリ。** 2 つ目のノートを作成する。`control-notes cli -- notes create --title "CLI note" --body "Created from terminal" --format json` を実行する。終了コード `0` と stdout に新しいノート ID とタイトルが含まれる。
- **証明。** 保存された両方のノートを `All notes` から再度開く。`control-notes browser snapshot --aria --path artifacts/create-note/list.aria.txt` と `control-notes browser screenshot --path artifacts/create-note/list.png` を実行する。アーティファクトに `Release checklist` と `CLI note` が表示される。

## Gotchas

- テキストボックスがフォーカスを持っている間に `n` を押すと、新しいエディタを開く代わりにその文字を入力する。
- タイトルは保存時にトリムされる。下書き入力値ではなく、レンダリングされたタイトルをアサートする。
- 保存ステータスだけでは証明として不十分である。リストからノートを再度開く。
- フィクスチャクリーンアップ中に `Release checklist` と `CLI note` を削除するが、その証明アーティファクトは保持する。
