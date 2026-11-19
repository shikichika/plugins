# CounterLoop

TLC は 3 状態、3 ステップに到達しました。チャートは 3 状態、3 本の矢印を描画します。

## ReachesThree が破られる
TLC の反例は tlc-ReachesThree.out にあります。3 ステップで構成されます:
0. 0
1. Up: 1
2. Up: 2
3. Back: 1
この実行は "1" と "2" の間を永久にループします。

## 成立するルール
NoDeadlock
