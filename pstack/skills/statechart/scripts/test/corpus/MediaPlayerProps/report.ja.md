# MediaPlayerProps

TLC は 12 状態、64 ステップに到達しました。チャートは 7 状態、13 本の矢印を描画します。
ステップ a call comes in は Playback と Volume を同時に変化させます。
ステップ the track ends は Playback と Volume を同時に変化させます。
ステップ you press stop は Playback と Volume を同時に変化させます。

## EventuallyPlays が破られる
TLC の反例は tlc-EventuallyPlays.out にあります。0 ステップで構成されます:
0. Playback Stopped, Volume Normal volume
この実行は "Playback Stopped, Volume Normal volume" で永久に停止します。

## QuietWhenStopped が破られる
TLC の反例は tlc-QuietWhenStopped.out にあります。1 ステップで構成されます:
0. Playback Stopped, Volume Normal volume
1. you turn it up: Playback Stopped, Volume Loud

## 成立するルール
TypeOK, NoDeadlock, EventuallyStops, StaysTyped
