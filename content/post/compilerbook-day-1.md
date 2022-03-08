---
title: "コンパイラ作成日記 1日目"
date: 2022-03-08T10:21:08+09:00
draft: false
tags: ["compiler", "compilerbook", "C"]
---

まとまった時間がとれたのでこれに挑戦する。

[低レイヤを知りたい人のためのCコンパイラ作成入門](https://www.sigbus.info/compilerbook)

# 環境構築

## M1　Mac編
M1 Mac で動くかどうかは書いていなかったのでとりあえず環境構築やってみた。

MacではDockerを使えとあったので、[付録3](https://www.sigbus.info/compilerbook#docker)に沿ってやってみた。

しかし、M1Macでは途中までは開発進めることができたが以下のエラーで詰まってしまった。

```shell
$ cc -o tmp tmp.s
tmp.s: Assembler messages:
tmp.s:1: Error: unknown pseudo-op: `.intel_syntax'
tmp.s:4: Error: operand 1 must be an integer register -- `mov rax,123'
```
思いっきり `intel_syntax` って書いてあるな 😇

あきらめて Intel Mac で開発する

## Intel　Mac編

Dockerのインストールから必要だった。

```shell
# docker install
$ brew install --cask docker

# 使うイメージをビルド
$ docker build -t compilerbook https://www.sigbus.info/compilerbook/Dockerfile
```

ちなみにこの Dockerfile 中身を見たがそんなたいしたことはしていなかった。

```docker
$ curl https://www.sigbus.info/compilerbook/Dockerfile
FROM ubuntu:latest
RUN apt update
RUN DEBIAN_FRONTEND=noninteractive apt install -y gcc make git binutils libc6-dev gdb sudo
RUN adduser --disabled-password --gecos '' user
RUN echo 'user ALL=(root) NOPASSWD:ALL' > /etc/sudoers.d/user
USER user
WORKDIR /home/user
```

これで開発スタートできる。

---

# ステップ1：整数1個をコンパイルする言語の作成

## 動かしてみる

最初はなにもわからないので写経してすすめていく。

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv) {
  if (argc != 2) {
    fprintf(stderr, "引数の個数が正しくありません\n");
    return 1;
  }

  printf(".intel_syntax noprefix\n");
  printf(".globl main\n");
  printf("main:\n");
  printf("  mov rax, %d\n", atoi(argv[1]));
  printf("  ret\n");
  return 0;
}
```

このコードを動かしてみる。

```shell
$ cc -o 9cc 9cc.c
$ ./9cc 123 > tmp.s
$ cat tmp.s
.intel_syntax noprefix
.globl main
main:
  mov rax, 123
  ret
$ cc -o tmp tmp.s
$ ./tmp
$ echo $?
123
```

動いた！！！。大学の授業などで最初にやる Hello World! を思い出す。

教本に以下のことが書かれている。

> 0〜255の範囲の123以外の数を与えてみて（Unixのプロセス終了コードは0〜255ということになっています）

というわけで範囲外の数字を input してみる。

```shell
$ ./9cc 1000 > tmp.s
$ cc -o tmp tmp.s
$ ./tmp
$ echo $?
232
```

今時点だとなぜこうなるのかわからないが範囲外のものを input するとエラーで止まるのではなく変な挙動になることがわかった。

## 自動テストの作成

テスト大事。最初にテストまであるのはとても親切。

## make によるビルド
あんまり Makefile 書くことがなかったので `.PHONY` というのは知らなかった。調べてみるとこれはダミー値で、サブコマンドとディレクトリやファイルと一致したときに動かなくなるのを想定して用意されているようだ。

- [Phony Targets (GNU make)](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html)
- [MakefileのPHONYで便利コマンドを書く - なるぽのブログ](https://yu-nix.com/blog/2021/8/27/makefile-phony/)

---
疲れたので1日目はここまで。久しぶりになにもわからない世界に入って楽しい！！