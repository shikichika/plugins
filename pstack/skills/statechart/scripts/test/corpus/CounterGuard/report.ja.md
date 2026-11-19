# CounterGuard

TLC は 2 状態、1 ステップに到達しました。チャートは 2 状態、1 本の矢印を描画します。
TLC は描画実行を終了コード 75 で停止したため、チャートは停止までに到達した状態のみを描画します。

## TypeOK が破られる
TLC は反例を発見しました。詳細は tlc-TypeOK.out にあります。

## チェックされなかったルール
Lookup, NoDeadlock
TLC は終了コード 75 で停止し、これらのルールをチェックしませんでした:
```
Error: Evaluating invariant Lookup failed.
Attempted to apply tuple
<<"zero", "one">>
to integer 3 which is out of domain.
Error: The behavior up to this point is:
State 1: <Initial predicate>
x = 0

State 2: <Next line 6, col 9 to line 6, col 18 of module CounterGuard>
x = 1

State 3: <Next line 6, col 9 to line 6, col 18 of module CounterGuard>
x = 2

Error: The error occurred when TLC was evaluating the nested
expressions at the following positions:
0. Line 12, column 11 to line 12, column 30 in CounterGuard
1. Line 12, column 11 to line 12, column 21 in CounterGuard
```
