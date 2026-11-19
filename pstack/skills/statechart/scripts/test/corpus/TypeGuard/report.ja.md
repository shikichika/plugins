# TypeGuard

TLC は 3 状態、2 ステップに到達しました。チャートは 3 状態、2 本の矢印を描画します。
TLC は描画実行を終了コード 76 で停止したため、チャートは停止までに到達した状態のみを描画します。

## TypeOK が破られる
最短パスは 2 ステップです:
0. 0
1. Next: 1
2. Next: 2

## チェックされなかったルール
NeedsTypeOK, NoDeadlock
TLC は終了コード 76 で停止し、これらのルールをチェックしませんでした:
```
Error: Evaluating invariant NeedsTypeOK failed.
Attempted to apply tuple
<<"zero", "one">>
to integer 3 which is out of domain.
Error: The behavior up to this point is:
State 1: <Initial predicate>
x = 0

State 2: <Next line 6, col 9 to line 6, col 27 of module TypeGuard>
x = 1

State 3: <Next line 6, col 9 to line 6, col 27 of module TypeGuard>
x = 2
```
