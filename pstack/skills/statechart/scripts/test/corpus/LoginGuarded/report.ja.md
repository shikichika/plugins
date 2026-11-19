# LoginGuarded

TLC は 8 状態、11 ステップに到達しました。チャートは 7 状態、10 本の矢印を描画します。
ステップ ThePasswordIsRight は Session と Tries を同時に変化させます。
ステップ ThePasswordIsWrong は Session と Tries を同時に変化させます。

## 成立するルール
TypeOK, SignedInWhileLocked, NoDeadlock
