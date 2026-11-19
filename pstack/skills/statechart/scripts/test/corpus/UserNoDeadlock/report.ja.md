# UserNoDeadlock

TLC は 4 状態、3 ステップに到達しました。チャートは 4 状態、3 本の矢印を描画します。

## NoDeadlock が破られる
最短パスは 1 ステップです:
0. 0
1. Next: 1

## CHECK_DEADLOCK が破られる
最短パスは 3 ステップです:
0. 0
1. Next: 1
2. Next: 2
3. Next: 3
