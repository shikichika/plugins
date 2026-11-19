# CounterFan

TLC は 25 状態、36 ステップに到達しました。チャートは 25 状態、36 本の矢印を描画します。

## NotDone が破られる
最短パスは 2 ステップです:
0. done=FALSE x=0
1. Pick: done=FALSE x=1
2. Finish: done=TRUE x=1

## 成立するルール
NoDeadlock
