# MediaPlayer

TLC は 12 状態、64 ステップに到達しました。チャートは 7 状態、13 本の矢印を描画します。
ステップ a call comes in は Playback と Volume を同時に変化させます。
ステップ the track ends は Playback と Volume を同時に変化させます。
ステップ you press stop は Playback と Volume を同時に変化させます。

## LoudWhenStopped が破られる
最短パスは 1 ステップです:
0. Playback Stopped, Volume Normal volume
1. you turn it up: Playback Stopped, Volume Loud

## 成立するルール
TypeOK, NoDeadlock, EventuallyStops
