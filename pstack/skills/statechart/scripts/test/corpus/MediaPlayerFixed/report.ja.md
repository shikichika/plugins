# MediaPlayerFixed

TLC は 11 状態、57 ステップに到達しました。チャートは 7 状態、13 本の矢印を描画します。
ステップ a call comes in は Playback と Volume を同時に変化させます。
ステップ you press stop は Playback と Volume を同時に変化させます。
ステップ the track ends は Playback と Volume を同時に変化させます。

## 成立するルール
TypeOK, LoudWhenStopped, NoDeadlock
