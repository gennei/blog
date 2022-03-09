---
title: "コンパイラ作成日記 2日目"
date: 2022-03-09T10:38:49+09:00
draft: false
tags: ["compiler", "compilerbook", "C"]
---

こちらの挑戦日記2日目

[低レイヤを知りたい人のためのCコンパイラ作成入門](https://www.sigbus.info/compilerbook)

2日目は以下のことをやった

- strtol 関数の調査
- トークナイザの導入
- エラーメッセージの改良
- 抽象構文木とスタックマシンのお勉強

## strol 関数について

> ポイントの一つとして、文字列に数字と無関係の文字が含まれている場合等、読み込みに失敗した場合に、第二引数のポインタにそのアドレスを格納できる点がある。これにより読み込みに失敗した箇所を正しく同定できる。この処理が不要な場合はNULLを指定すればよい。

[strtol による整数の読み取り · ソフトウェアII](https://www.gavo.t.u-tokyo.ac.jp/~dsk_saito/lecture/software2/misc/misc02.html) より引用。

input の数字の桁数が違っても問題なく読めるのはなんでだろうと思ったら、`strtol` という関数は数字を読めるとこまで読むので問題がないようだ。なるほど。

## エラーメッセージの改良
```shell
$ ./9cc "1+3++" > tmp.s
1+3++
    ^ 数ではありません

$ ./9cc "1 + foo + 5" > tmp.s
1 + foo + 5
    ^ トークナイズできません
```

power assert を思い出す表示。

```c
// エラー箇所を報告する
void error_at(char *loc, char *fmt, ...) {
  va_list ap;
  va_start(ap, fmt);

  int pos = loc - user_input;
  fprintf(stderr, "%s\n", user_input);
  fprintf(stderr, "%*s", pos, " ");
  fprintf(stderr, "^ ");
  vfprintf(stderr, fmt, ap);
  fprintf(stderr, "\n");
  exit(1);
}
```

この実装箇所で `int pos = loc - user_input;` はどこまで読み取ってエラーになったのかを計算しているのはわかるが、文字列同士の演算ってどうなるんだっけ。ここがよくわからなかった。

## 抽象構文木とスタックマシンのお勉強

1回読んだだけではコンセプトはわかったが具体的にどうやって実装して動作するのかイメージができなかったのでもう一度読みながら動作を観察してみよう。

明日も引き続き頑張ろう。