---
title: "ChatGPT4 を使って Github Actions でサイトのパフォーマンス計測やってみた"
date: 2023-03-18T23:40:00+09:00
draft: false
tags: ["AI", "ChatGPT", "performance"]
---

サイトのパフォーマンスを継続的に計測して可視化してみたいなあと思ったので今話題のChat-GPTに聞いてみた。

<img width="900" height="auto" src="/img/try-chat-gpt4/q1.png" alt="パフォーマンスを計測する方法はありますか">

<img width="900" height="auto" src="/img/try-chat-gpt4/q2.png" alt="lighthouseを動かすにはどうしたらいいか">

---

やってみた結果、提示してくれた GithubActions はうまく動かなかった。バージョンが違ったり存在しないパラメータを渡していたりして想定した動きはしなかった。だが、使用するライブラリなどは正しかったり、途中まではあっているのでそこは人間が直していけばよさそう。

試行錯誤したコミットが見えるPRは[こちら](https://github.com/gennei/blog/pull/9)

---

ChatGPTだけではうまくいかなかったので以下人間が頑張った。

## lighthouseでのサイトパフォーマンス計測の方法

https://github.com/GoogleChrome/lighthouse-ci を使用する。計測対象はPRを作成したブランチのこのサイトを対象にしてみた。

公式ドキュメントに従って workflow を作成する

```yaml
name: Lighthouse

on:
  pull_request:
    branches:
      - master

jobs:
  lighthouse:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
        with:
          ref: ${{ github.event.pull_request.head.sha }}
          submodules: true

      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 16
      
      - name: Setup Hugo (ここは自分のサイトのためのもの)
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.101.0'
          extended: true

      - name: Build (ここは自分のサイトのためのもの)
        run: HUGO_ENV=production hugo --minify

      - name: run Lighthouse CI
        run: |
          npm install -g @lhci/cli@0.11.x
          lhci autorun
```

lighthouse の設定ファイル `lighthouserc.json` を作成する。今回は 上記の workflow で build した生成物に対して実行するので、url を localhost にして ディレクトリを build で生成物が作られる `./docs` を指定する。

```json
{
  "ci": {
    "collect": {
      "url": ["http://localhost/index.html"],
      "staticDistDir": "./docs"
    },
    "assert": {
      "preset": "lighthouse:recommended"
    },
    "upload": {
      "target": "temporary-public-storage"
    }
  }
}
```

この2つの設定を追加するだけでPR作成、プッシュしたときに lighthouse の計測ができるようになる。

### PRの status を更新をする

PRを作成、Pushしたときに エラーがあった場合はGitHubで知れると便利なので PR の status を更新できるようにする。
今回は GitHub App を使用した。[Lighthouse CI](https://github.com/apps/lighthouse-ci)。リポジトリの管理画面で手順の通り環境変数を追加して workflow に以下の2行を追加

```diff
      - name: run Lighthouse CI
        run: |
          npm install -g @lhci/cli@0.11.x
          lhci autorun
+       env:
+         LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}
```

これで CI で fail したときにエラーを表示してくれるようになる。

<img src="https://user-images.githubusercontent.com/6353363/226109957-a374f655-00d5-45e1-9503-c7e6116e003e.png" width="90%">

details をクリックすると lighthouse の実行結果が見える 🎉🎉🎉

<img src="/img/try-chat-gpt4/lighthouse-result.png" width="90%">

とりあえず今回はここまで。