### PR を開く

他のすべてのプレイブックの最後で呼び出される。

トランクから生やした git worktree で作業し、無関係な作業は持ち込まないこと。小さく着地可能なコミットにリベースする。コミット前に `cursor-team-kit` の `/deslop` を差分に適用する。

タイトル: `type(scope): subject`。変更を担う実シンボルがあればその名前を入れる。本文: 40 行未満の 1 分ブリーフィング。以下の `##` 見出しをこの順に用い、空のものは落とす: `## Why`、`## What changed`、`## Scope`、`## Tradeoffs`（レビュアーがそうでなければ尋ねるであろう却下済みの代替案のみ）、`## Blast Radius`、`## Verification`。

フォローアップはベースブランチで積む。実行用の PR ツールを使い、`draft: false` で PR を ready で開く。無い場合は `gh pr create` を `--draft` 無しで用いるか、Origin の CLI がリポジトリを解決できるときは `origin pr create --status open` を使う。PR を開くこと自体は babysit を起動しない。
