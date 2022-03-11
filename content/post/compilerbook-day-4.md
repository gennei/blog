---
title: "コンパイラ作成日記 4日目"
date: 2022-03-11T17:32:53+09:00
draft: false
tags: ["compiler", "compilerbook", "C"]
---

こちらの挑戦日記4日目

[低レイヤを知りたい人のためのCコンパイラ作成入門](https://www.sigbus.info/compilerbook)

# 今日やったこと
- 比較演算子の実装


## 比較演算子の実装

教本では一度に実装していたが、一度に大きく変更するとわけわからなくなるので一文字演算子だけ実装してみた。

1. `<`, `>` だけ実装した
2. 2文字以上扱えるようにした
3. `==`, `!=` を実装
4. `<=`, `>=` を実装

教本にあるこれが読めるようになったのでそんなに難しくなかった。

```shell
expr       = equality
equality   = relational ("==" relational | "!=" relational)*
relational = add ("<" add | "<=" add | ">" add | ">=" add)*
add        = mul ("+" mul | "-" mul)*
mul        = unary ("*" unary | "/" unary)*
unary      = ("+" | "-")? primary
primary    = num | "(" expr ")"
```


