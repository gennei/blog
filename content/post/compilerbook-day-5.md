---
title: "コンパイラ作成日記 5日目"
date: 2022-03-17T21:15:22+09:00
draft: false
tags: ["compiler", "compilerbook", "C"]
---

こちらの挑戦日記5日目

[低レイヤを知りたい人のためのCコンパイラ作成入門](https://www.sigbus.info/compilerbook)

リポジトリはこちら。 [gennei/compilerbook-challenge](https://github.com/gennei/compilerbook-challenge)

# 今日やったこと

## 分割コンパイルとリンク

- 1つのファイルで管理するのがしんどくなってきたので小さくする
- 9cc.cという1つのファイルを、以下の5つのファイルに分割する。
    - `9cc.h`: ヘッダファイル
    - `main.c`: `main`関数
    - `parse.c`: パーサ
    - `codegen.c`: コードジェネレータ
    - `tokenize.c`: トークナイズ

やっと分割できる。分割してみてわかったのは header file というものを久しく書いていなくて懐かしかった。header file はなんとなく interface に似ている。存在する function はありながら実態は別のファイルに記述されている、そういうところが似ている。  
コンパイルするので実装漏れがあってもエラーになるのであんまり不安はないがグローバル変数を引き回すのはやはり分かりづらいなあ。

とりあえず今日は分割まで。