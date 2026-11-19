# TwoPhase

TLC は 76 状態、192 ステップに到達しました。チャートは 10 状態、16 本の矢印を描画します。
チャートは、同一のチャート状態に留まる 59 ステップを描画しません。
ステップ RMPrepare は r1 と r2 のうち一方のみを変化させます。
ステップ RMChooseToAbort は r1 と r2 のうち一方のみを変化させます。
ステップ RMRcvAbortMsg は r1 と r2 のうち一方のみを変化させます。
ステップ RMRcvCommitMsg は r1 と r2 のうち一方のみを変化させます。

## TCConsistent が破られる
最短パスは 5 ステップです:
0. Manager init, r1 working, r2 working
1. RMChooseToAbort: Manager init, r1 working, r2 aborted
2. RMPrepare: Manager init, r1 prepared, r2 aborted
3. TMRcvPrepared: Manager init, r1 prepared, r2 aborted
4. TMCommit: Manager done, r1 prepared, r2 aborted
5. RMRcvCommitMsg: Manager done, r1 committed, r2 aborted

## 成立するルール
TPTypeOK, NoDeadlock
