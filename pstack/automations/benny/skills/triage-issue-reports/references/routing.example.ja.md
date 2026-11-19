# ルーティングマップ例

このファイルを `.cursor/automations/benny/` の外、例えば `.cursor/benny/routing.md` にコピーし、すべてのプレースホルダを置き換えること。`routing.map_path` をそのコピーに向けること。パックの更新がコピーを上書きしてはならない。

トリアージスキルはこれをデータとして扱う。ルートの選択には、報告または原因追跡からの証拠が必要である。キーワード一致だけでは不十分。

```yaml
routes:
  - name: "billing-example"
    match:
      product_areas:
        - "billing-area-placeholder"
      code_paths:
        - "billing-code-path-placeholder"
      error_signatures:
        - "billing-error-placeholder"
    destination:
      slack_channel: "billing-channel-placeholder"
      tracker_team: "billing-team-placeholder"
    owners:
      - "billing-owner-placeholder"
    allow_feature_owner_ping: false

  - name: "desktop-example"
    match:
      product_areas:
        - "desktop-area-placeholder"
      code_paths:
        - "desktop-code-path-placeholder"
      error_signatures:
        - "desktop-error-placeholder"
    destination:
      slack_channel: "desktop-channel-placeholder"
      tracker_team: "desktop-team-placeholder"
    owners:
      - "desktop-owner-placeholder"
    allow_feature_owner_ping: false

fallback:
  destination: ""
  owners: []
  allow_feature_owner_ping: false

ping_policy:
  default: "off"
  allow:
    - "configured-feature-owner"
    - "confirmed-regression-author"
  deny:
    - "broad-on-call-group"
    - "unverified-owner"
```

## ルール

- 一致しない報告を受け入れるチームがない限り、`fallback.destination` は空のままにする。
- 安定したプロダクト領域、コードパス、エラーシグネチャを使う。
- 公開コピーにプライベートデータを含めない。
- 公開予定のサンプルに生のユーザー ID やチャンネル ID を貼らない。
- 対象チームが合意するまで、機能オーナーのメンションは無効にしておく。
- リルートは報告者にどこへ行けばよいかを伝えるもの。オートメーションが別チャンネルへ投稿してはならない。
