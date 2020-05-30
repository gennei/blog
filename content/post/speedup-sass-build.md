---
title: "Sass の build 時間 を 1/16 にした"
date: 2020-05-30T16:00:00+09:00
draft: false
tags: ["Sass", "Compass", "LibSass"]
---

## tl;dr
- sass で書いたものを css で変換するのに Ruby製の [Compass](https://github.com/Compass/compass) を使っていたが遅くて困っていた
- Ruby 製の Compass をやめて LibSass の変更
- 結果、**160秒** かかっていたのが **10秒** になった

<!--more-->

## 背景
仕事で Ruby製の Compass を使って scss を css に変換していた。ただ、Ruby製の Compass は遅いとのことが以前から言われた。またすでにメンテナンスされていないため使うのをやめたいと思っていた。
置き換えるには [LibSass](https://github.com/sass/libsass) が主流になっておりこちらに移行しようとした。

しかし、Compass には便利な機能で、ベンダープレフィックスやmixinがあり、単純に Compass を捨てて Libsass に移行ができなかった。

## 調査
移行事例の調査をした

- [Compass の送別会](https://tech.pepabo.com/2018/03/15/bye-bye-compass/)
- [脱Compassしたいけど@importでCSSスプライトを作る機能は捨てがたかったので作った話](https://tech.gmo-media.jp/post/155044941185/create-css-sprites-library-like-compass)
- [compassからlibsassへ。gulpの設定を見直して大幅速度UP！](https://www.okushin.co.jp/kodanuki_note/2017/10)

スプライトをやめて、mixin をやめて Compass の依存を排除してから移行しているところや、[compass-mixins](https://github.com/Igosuki/compass-mixins) を使用して compass の機能を残しながら Compass から LibSass へ移行している事例があった

## 対応

調査したもの踏まえて以下の対応をした

### node-sass へ置き換え

`Compass` をやめて [node-sass](https://github.com/sass/node-sass) をインストール

### @import "compass" を使えるようにする

[compass-mixins](https://github.com/Igosuki/compass-mixins) をインストールして使う

### gulpfile の見直し

`@compass` を使えるように先程インストールしたものを読み込む

```js
gulp.task('css:tanspile', () => {
    return gulp.src('/path/to/src/**.scss')
        .pipe(sass({
            includePaths: ['./node_modules/compass-mixins/lib/'], 
            force: true,
            environment: 'production',
            outputStyle: 'compressed'
        }))
        .pipe(gulp.dest('/path/to/dest/'))
});
```

### ファイルパスの見直し
手元の開発環境では、今まではファイルパスが　scss　のディレクトリのルートからのパスだったが、各　scss　ファイルからのパスになったので修正した

## パフォーマンス計測

- before 160秒
- after  10秒

対応前はファイル数が増えるたびに遅くなりCPUパワーを使っていた。deploy 時間も短くなりリリースのときの時間が短くなったｌ

## 結論
- Issue を着手してから３年ぐらいかかってしまったが、本来はもう少し早く解決できた問題だった
- ただ３年の間に Gulp についてや Sass についての知識が自分の中に蓄積されたことによって問題の見え方が変わった
- 開発環境でファイルを変更してから画面に反映されるまで30秒ぐらいかかっていたのがすぐに反映されるようになり開発体験が向上した
