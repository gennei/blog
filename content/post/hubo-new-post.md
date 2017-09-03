---
title: "Hugo + github-pages でブログを作った"
date: 2017-09-03T15:05:48+09:00
draft: false
---

はてなブログを使っていたが、読み込みが遅い、web で編集をするのがめんどうという理由でブログを更新しなくなった。  
そこでローカルで編集ができて、Markdownで書ける方法を探した。どうやら[いくつかの方法](https://awe-some.net/2017/01/statatic-site-generator/)があるようだが、hugo の評判がいいので hugo を採用した。

---

## 導入ログ

### hugo をインストール

```sh
$ brew install hugo
```

### blog を作る

```sh
$ hugo new site blog
```
これだけで作れたのでとても楽だった。

### テーマをダウンロード
```
$ cd ./themes
$ git clone https://github.com/halogenica/beautifulhugo.git beautifulhugo
```

### エントリを書く
```sh
$ hugo new post/entry-title
```

これで `content/post/entry-title.md` という記事ができあがる。

### 手元で確認する
```
$ hugo server -t beautifulhugo -D -w
```

これで `http://localhost:1313/` 確認できる。

### 公開用のHTMLを作成する

```
$ hugo -t beautifulhugo
```

これで `./public` に 公開用のHTMLが作成される。

### 公開用のディレクトリを変更する
`config.toml` の設定を書き換える。
```toml
publishDir = "docs"
```
としておくと `./docs` に出力してくれる。こうしておけば github-pages のディレクトリと一致するので github.io で公開できる。


簡単だ。
