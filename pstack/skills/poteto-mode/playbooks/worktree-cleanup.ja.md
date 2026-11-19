### worktree とシミュレーターのクリーンアップ

1. スナップショットと監査。まず `df -h /` を記録し、`scripts/worktree-audit.sh [-b <pr-base-ref>] [repo-path]` を実行する（principle-build-the-lever）。`-b` 無しでは `origin/HEAD` に対して `MERGED` を確認するので、PR が別ブランチをターゲットにしているなら `-b origin/<branch>` を渡す。パスは `git worktree list` から読む。手入力ではない。手入力の `myrepo-worktrees/x` では `.cursor/worktrees/myrepo/x` に置かれたものを見落とすからだ（principle-encode-lessons-in-structure）。worktree ごとにサイズ、経過時間、マージ状態、未コミット作業、PR の状態、最新で触れたチャットを分類し、バケットを提案する。トランスクリプトスキャンは遅いので、バックグラウンド化する。
2. バケットは助言であって許可ではない。pin されたチャットとアクティブなチャットが真の成果物だ（principle-prove-it-works）。その集合をユーザーまたはサイドバーから取得し、候補すべてと突き合わせる。レバーは、ユーザーが pin していた worktree を `safe` とマークしたことがある。pin 済み集合が優先される。
3. 削除前に使用状況を検証する。`verify-recent-chat` の各行や疑わしいものについて、サブエージェントをファンアウトしてトランスクリプトを読ませ、チャットが pin されているか進行中か、どの worktree に触れたかを報告させる（principle-guard-the-context-window、トランスクリプトはかさばる）。pin されたチャットは、バックグラウンドのサブエージェント経由で兄弟 worktree に arena や repro tree を生成し、サイドバー上に名前が出ない場合でもそれらは使用中だ。Subagents セクションに従い、ジョブに応じて各サブエージェントの推論レベルを選ぶ。
4. 不可逆な損失には立ち止まる。`wip:N` は追跡対象の未コミット編集 N 件。クリーンな worktree の削除はブランチから復元可能だが、未コミット作業は失われる。まず diff を示し、判断を仰ぐ。`scratch:N` は未追跡の使い捨てで、安全に落とせるが、ファイル名を挙げる。Autonomy に従い、クリーンかつマージ済みかつ未使用なものは進める。`wip` と使用中のものは停止する。
5. 確定したセットを剪定する。パスごとに `git worktree remove --force <path>`。ディレクトリが無視対象ビルド成果物で残るなら `rm -rf` し、その後 `git worktree prune` を実行する。ブランチ参照は残るのでコミットは失われない。`df -h /` と再リストで確認する。
6. シミュレーターとその他の回収。シミュレーターは通常、次に大きなリターンをもたらす。`xcrun simctl --set testing delete all`（XCTestDevices のクローン）、`xcrun simctl delete unavailable`、古いランタイムに対しては `xcrun simctl runtime list` のあと `runtime delete <id>`。必要に応じてさらに: Xcode の `DerivedData` と `iOS DeviceSupport`、`~/Library/Application Support/Cursor`（`state.vscdb.backup`、および workspace として開いたフォルダ名の `<root>` が膨張する `snapshots/roots/<root>`）、パッケージキャッシュ（pnpm、uv、brew、yarn）。ユーザーが保持を指示していないキャッシュのみ消去する。

これは、取りこぼしを捉えるコードレビューなしでユーザーの状態を削除する唯一のプレイブックなので、上記のゲート群がレビューに相当する。

**返信:** 作業前後の `df -h /` と回収容量、剪定した worktree、保持したものについて 1 行の理由（どのチャットが使用中か、未コミット作業か）。
