# DieHard

TLC は 16 状態、96 ステップに到達しました。チャートは 16 状態、58 本の矢印を描画します。
チャートは、同一のチャート状態に留まる 38 ステップを描画しません。

## NotSolved が破られる
最短パスは 6 ステップです:
0. big=0 small=0
1. FillBig: big=5 small=0
2. BigToSmall: big=2 small=3
3. EmptySmall: big=2 small=0
4. BigToSmall: big=0 small=2
5. FillBig: big=5 small=2
6. BigToSmall: big=4 small=3

## 成立するルール
TypeOK, NoDeadlock
