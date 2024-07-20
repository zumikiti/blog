---
title: "vim で箇条書きの連番を簡単に入力する"
date: 2024-07-20T07:17:32+09:00
---

以下のような箇条書きを作りたい場合

```md
1. hoge
2. hoge
3. hoge
4. hoge
5. hoge
6. hoge
7. hoge
8. hoge
9. hoge
10. hoge
11. hoge
12. hoge
```

vim では、以下の手順で行うことができる。

1. `1.` の行を入力して、必要な分複製する。
2. ビジュアルモードでカウントアップさせたい行を選択する
3. `g` と入力後に、 `CTRL + a` と入力する

以上

---

なお、`g` を事前に入力しない場合は、一つだけカウントアップした数字が複製される。

```md
1. fuga
2. fuga
2. fuga
2. fuga
2. fuga
2. fuga
2. fuga
2. fuga
2. fuga
2. fuga
2. fuga
2. fuga
```
