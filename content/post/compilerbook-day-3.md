---
title: "コンパイラ作成日記 3日目"
date: 2022-03-09T21:15:22+09:00
draft: false
tags: ["compiler", "compilerbook", "C"]
---

こちらの挑戦日記3日目

[低レイヤを知りたい人のためのCコンパイラ作成入門](https://www.sigbus.info/compilerbook)

リポジトリはこちら。 [gennei/compilerbook-challenge](https://github.com/gennei/compilerbook-challenge)

# 今日やったこと
- 再帰下降構文解析の復習
- スタックマシンの概念の復習
- 四則演算のできる言語の作成
- 単項プラスと単項マイナス
- 比較演算子のアイディアを読むところまで

commit: [gennei/compilerbook-challenge@7a25e4a](https://github.com/gennei/compilerbook-challenge/commit/7a25e4a48fb8782151282ab90580dde39641a886)

今日は引っ越しの都合でワクチン接種券が届いていなかったので発券をした。あと運転免許証の住所変更をやっていなかったので警察署へ手続きへ行った。

毎日終わりに日記を書こうと思っていたけど、昨日の分書けていなかったので午前中に記事を書いた。

---

## 昨日の復習

昨日再帰下降構文解析とスタックマシンの文章を読んでいたが途中で頭痛がしたので流し読みして切り上げた。今日は復習するところから始めた。

今日読み直したらそんなに難しいことではないのではという気持ちになった（理解がちゃんとできているかは怪しい）

四則演算を実装する箇所の以下のコードを読んで感動した。

```c
// 抽象構文木を下りながらコード生成
Node *node = expr();
gen(node);
```

ちゃんと責務が分割され、シンプルなルールで構文木を作れるようになるとこんなにもソースコードが読みやすくなるのかと。

## 単項プラス、単項マイナス

単項の+-の導入であれば以下のルールになるのも面白かった。

### Before
```
expr    = mul ("+" mul | "-" mul)*
mul     = primary ("*" primary | "/" primary)*
primary = num | "(" expr ")"

```
### After
```
expr    = mul ("+" mul | "-" mul)*
mul     = unary ("*" unary | "/" unary)*
unary   = ("+" | "-")? primary
primary = num | "(" expr ")"
```

結局ASTで表現できるようになると評価する順番は意識する必要はあるが木構造にどのように落とし込む必要があるのかということだけを意識すればよくなるので、今まで別々のものが同じ抽象度で扱えるようになったのはとてもわかりやすくなった。先頭から読んでいって、先読みして戻ってくるような発想をしなくてもよさそう。

最近 [YAPC::Japan::Online 2022](https://yapcjapan.org/2022online/) で見た発表でも似たような話をしていた気がするなあ。

[ReDoS 検出の最先端 recheck の紹介 / State of the Art of ReDoS Detection - Speaker Deck](https://speakerdeck.com/makenowjust/state-of-the-art-of-redos-detection-241e16d2-ad8e-462c-8c0e-b6a95a735d59)

<iframe class="speakerdeck-iframe" frameborder="0" src="https://speakerdeck.com/player/7e913e6530e3464686edc3f62668926a?slide=1" title="ReDoS 検出の最先端 recheck の紹介 / State of the Art of ReDoS Detection" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true" style="border: 0px; background: padding-box padding-box rgba(0, 0, 0, 0.1); margin: 0px; padding: 0px; border-radius: 6px; box-shadow: rgba(0, 0, 0, 0.2) 0px 5px 40px; width: 560px; height: 314px;" data-ratio="1.78343949044586"></iframe>

たぶん違うんだろうけど一瞬これと関連するのかなと思った。正規表現の先読みとASTの解析は違うんだろうなあ。

## 比較演算子

これまでは1文字の演算子のみだったが2文字のものを扱っていく。node に文字列長を持つようになっているが、数字のnodeは0文字としていたなあ(数字のときは文字列長を意識しないからかな)。

---

明日は比較演算子からやっていく。仕事ではないので無理をせず眠くなったら昼寝をするし、散歩にも出よう。
