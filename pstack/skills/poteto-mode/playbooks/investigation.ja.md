### Investigation

**あなたは答えを所有する。計画、ルート、書く。**

調査要求は読み取り専用である。それらは引用された説明または推奨を生成し、コード変更ではない。

1. 質問が関するコードをトレースする。動機の質問の場合、**why** スキル経由でもルートする。
2. スループットチェックポイントは 1 行のまま: `throughput checkpoint: n/a, read-only investigation`。
3. 出力を生成する (Overview / Key Concepts / How It Works / Where Things Live / Gotchas)、または要求が代替案間の決定の場合、トレードオフテーブル付きの推奨。
4. 返信に **unslop** スキルを適用する。

PR なし、ベビーシットなし、調査がコード変更に先行する場合を除き `architect` なし。先行する場合、ユーザーに渡し、Bug fix または Feature に再ルートする。

**返信:** 調査出力。「確信があるか」の答えには、理由付きで本当の判断を含める。前提が間違っている場合は押し返す (Autonomy を参照)。
