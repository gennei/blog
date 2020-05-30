---
title: "Hugo の build を Github Actions で実行するようにした"
date: 2020-01-02T21:37:37+09:00
draft: false
tags: ["github", "hugo", "GithubActions"]
---

今までこの blog を更新するときは、markdown で記事を書いて、その後 `$ hugo -t beautifulhugo` コマンドで生成した記事を github へ push していた。この build flow をどこかのタイミングで CircleCI などで実行するようにしようと思っていた。昨年 Github Actions が公開され気になっていたので暇な時間で作業してみた。

<!--more-->

## 手順
1. github actions の workflow を作成
1. deploy key の作成
1. 動作確認

### github actions の workflow を作成

ここを参考に作った。
- https://github.com/peaceiris/actions-hugo#getting-started
- https://qiita.com/kaakaa_hoe/items/8fc2cfc2e16093cc7264


```yml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@v1
      with:
        submodules: true
    # ここを true にしておくと git submodules のあるものもまとめて checkout してくれる

    - name: Setup Hugo
      uses: peaceiris/actions-hugo@v2
      with:
        hugo-version: '0.62.1'

    - name: Build
      run: hugo -t beautifulhugo

    - name: Deploy
      uses: peaceiris/actions-gh-pages@v2
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./docs
```

hugo の 生成したファイルの出力先が `./docs` なので deploy するときの `PUBLISH_DIR` はこれに合わせている。  
今回の GithubActions に合わせて `gh-pages` ブランチで運用するようにした。別ブランチにすることで master ブランチの変更の無限ループも回避できるので一石二鳥だった。

### deploy key の設定

GithubActions から github へ deploy するときはこちらを参考にした  
https://github.com/peaceiris/actions-gh-pages#getting-started


```
$ ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
```

これで生成したものを github へ登録。これで GithubActions で build したものを `gh-pages` ブランチへ push することができるようになった。

### 動作確認

このような記事を書いて master ブランチへ push した結果問題なく build して deploy できた。
![](/img/github-actions-result.png)

## 参考リンク
- [新しくなったGitHub ActionsでHugoブログのデプロイをしてみた](https://qiita.com/kaakaa_hoe/items/8fc2cfc2e16093cc7264)
- [peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo)
