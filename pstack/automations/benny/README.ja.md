# benny

benny は Slack のイシュー報告向けに 2 つの cursor オートメーションを提供する。1 つは各報告をトリアージする。もう 1 つは確認されたバグを再現し、小さなドラフト修正を用意することもある。

このディレクトリ内のファイルは、休眠状態のセットアップ用およびオートメーションソースである。スラッシュスキルとしては表示されない。

## セットアップ

1. cursor を [`FOR_AGENTS.md`](./FOR_AGENTS.md) に向け、ターゲットリポジトリ名を指定する。
2. セットアップに、このディレクトリ全体をターゲットの `.cursor/automations/benny/` にマージさせる。配布先にのみ存在するファイルは保持し、ローカルの編集を上書きせずに競合を確認すること。
3. セットアップに、ターゲットリポジトリの `.cursor/settings.json` で共有依存のために pstack を有効化させる:

```json
{
	"plugins": {
		"pstack": { "enabled": true }
	}
}
```

4. ユーザー所有の設定はコピーしたパックの外、例えば `.cursor/benny/` に置くこと。[`configuration.example.yaml`](./templates/configuration.example.yaml) と [`feature-map.example.md`](./skills/reproduce-and-fix-issues/references/feature-map.example.md) を自分の環境向けに調整する。
5. どちらのオートメーションを有効化する前にも、`.cursor/settings.json`、`.cursor/automations/benny/`、およびシークレットを含まない設定ファイルをコミットする。
6. 各オートメーションの新規ドラフトをレビューするか、既存のオートメーションをエディタで更新する。その後、無害なテスト報告を送り、ソースチャンネル上の投稿がすべて元のスレッド内に留まることを確認する。
