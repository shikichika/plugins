# TwoPhaseFixed

TLC は 56 状態、142 ステップに到達しました。チャートは 10 状態、12 本の矢印を描画します。
チャートは、同一のチャート状態に留まる 45 ステップを描画しません。
ステップ RMPrepare は r1 と r2 のうち一方のみを変化させます。
ステップ RMChooseToAbort は r1 と r2 のうち一方のみを変化させます。
ステップ RMRcvAbortMsg は r1 と r2 のうち一方のみを変化させます。
ステップ RMRcvCommitMsg は r1 と r2 のうち一方のみを変化させます。

## 成立するルール
TPTypeOK, TCConsistent, NoDeadlock
