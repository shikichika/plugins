---
name: unslop
description: あらゆる文章から AI の兆候を取り除く。
disable-model-invocation: true
---

# Unslop

テキストを編集して AI パターンを除去する。以下のパターンを走査し、書き直す。意味を保ち、意図したトーンに合わせる。

ルール番号は他のスキルが引用する安定した ID である。削除されたルールは欠番として残る。

3. 事実を追加しない末尾の「-ing」句（「highlighting...」「ensuring...」）を使わない。
5. 曖昧な出典（「experts believe」）を使わない。情報源を名指すか削除する。
7. AI 語彙を使わない: additionally、crucial、delve、enhance、foster、landscape、pivotal、showcase、testament、underscore、vibrant。
8. 「serves as」「stands as」「boasts」「features」ではなく「is」や「has」を使う。
9. 「not just X, but Y」を使わない。要点を直接述べる。
10. 考えを無理に 3 つにまとめない。
11. 物には 1 つの名前を。それを繰り返す。
12. X と Y が実際の尺度上にない限り「from X to Y」を使わない。
13. em ダッシュ、en ダッシュ、ダッシュとして使うハイフンを禁止。代用の括弧も禁止。ピリオドかカンマを使う。
14. コロンはリストや例の前に置く。接続詞として文中には使わない。
15. 固有名詞や略語をすべて太字にしない。
16. 行を言い換えるだけの太字ラベルとコロン（「**Performance:** Performance improved...」）を使わない。ピリオドで終わり新しい詳細を加える太字の先頭句は問題ない。
17. 見出しは文頭大文字（sentence case）にする。
18. 装飾的な絵文字を使わない。
19. ストレートクォートを使う。
20. チャットボット的な言い回し（「I hope this helps!」「Let me know if...」）を使わない。
22. 賛辞や同意の書き出しを使わない。直接応答する。
23. 無駄を切る。「In order to」は「to」になる。「it is important to note that」は削除する。
24. ぼかしは最大 1 つ: 「may」。
25. 一般的な結論を使わない。具体的な計画や事実を述べる。
26. 抽象的な比喩名詞を使わない: substrate、wedge、vector、nexus、primitive、harness、surface、scaffolding、paradigm、ratchet、north star、flywheel。具体語を使う。
27. どう感じるかではなく、何をするかを述べる。仕組みや数値を名指す。別のプロジェクトのドキュメントにそのまま現れうる文は削る。
28. 1 文に 1 つの考え。読者が読み直す必要のある文は分割する。
29. 能動態を使う。行為者が不明か関係ない場合を除き、行為者を名指す。
30. 副詞を削るか、より強い動詞を使う。数値を示す。
31. 平易な語を使う: 「utilize」や「leverage」ではなく「use」。
32. 文字通りの表現があるのに比喩、格言、擬人化されたコード、比喩的な動詞を使わない。
33. 冠詞と動詞を備えた完全な文を書く。矢印と略語を綴り切る。
