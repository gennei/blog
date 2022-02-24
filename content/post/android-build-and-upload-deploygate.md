---
title: "コマンドラインからAndroidアプリをビルドして deploygate へアップロードする"
date: 2022-02-24T20:15:00+09:00
draft: false
tags: ["android", "deploygate"]
---

# Androidのコマンドラインからのビルドについて調べた

とりあえず公式ドキュメントを読む

[コマンドラインからアプリをビルドする  |  Android デベロッパー  |  Android Developers](https://developer.android.com/studio/build/building-cmdline?hl=ja)

どうやらビルドタスク一覧は以下のコマンドで見れるようだ

```sh
./gradlew tasks
```

タスクから必要なデバッグビルドの作成方法を見つけたのでコマンドラインから実行する

```sh
./gradlew assembleDebug
```

# deploygate への配信

まずは公式ドキュメントを読む

[コマンドラインツール](https://docs.deploygate.com/docs/cli)

## コマンドラインツールのインストール

https://deploygate.com/cli を読むとターミナルで curl を実行するように書いてあるので実行する

```sh
$ curl -sSL https://deploygate.com/cli/install/10d09d54 | bash
```

```sh
$ dg -v
dg 0.8.4
```

無事インストールできた

## deploygate のログイン情報を作成

```sh
$ dg login
```

これでログイン情報を作成できる

## アップロードする

アップロードは以下のドキュメントに書いてある

[アプリのビルドとアップロード](https://docs.deploygate.com/docs/cli-deploy)

```sh
$ dg deploy ./path/to/app.apk
```

これで生成した apk アップロードできた。

# まとめ

Makefile にまとめた

```Makefile
build-apk:
	./gradlew assembleDebug

upload-to-deploygate:
	dg deploy ./path/to/app.apk
build-and-deploy: build-apk upload-to-deploygate
```

これでコマンドラインから build して upload するのができるようになった 🎉🎉🎉

```sh
$ make build-and-deploy
```

# 残り

あとは Bitrise, CircleCI, GitHub Actions などで自動化したいのでそれについては別途調べる