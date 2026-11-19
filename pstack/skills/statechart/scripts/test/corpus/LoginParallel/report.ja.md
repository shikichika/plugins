# LoginParallel

TLC は 12 状態、29 ステップに到達しました。チャートは 7 状態、10 本の矢印を描画します。
ステップ ThePasswordIsWrong は Session と Tries を同時に変化させます。
ステップ ThePasswordIsRight は Session と Tries を同時に変化させます。

## SignedInWhileLocked が破られる
最短パスは 5 ステップです:
0. Session Signed out, Tries No wrong tries
1. ThePasswordIsWrong: Session Signed out, Tries 1 wrong try
2. ThePasswordIsWrong: Session Signed out, Tries 2 wrong tries
3. ThePasswordIsWrong: Session Signed out, Tries Locked out
4. YouSubmitAPassword: Session Checking the password, Tries Locked out
5. ThePasswordIsRight: Session Signed in, Tries Locked out

## 成立するルール
TypeOK, NoDeadlock
