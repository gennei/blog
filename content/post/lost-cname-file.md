---
title: "GitHub Pages で公開するための CNAME ファイルが消えてしまうための対策"
date: 2022-02-05T16:00:00+09:00
draft: false
tags: ["github", "CI", "CNAME"]
---

このブログは現在、GitHub Pages を使って公開されており、ドメインは GitHub が提供しているカスタムドメイン機能を使用している。

GitHub Pages のドメインをあてるには Rootに `CNAME` というファイルが必要になる。

参考: https://github.com/gennei/blog/blob/gh-pages/CNAME

## 問題
現在、GitHub Actions を使って hugo のビルド結果を `gh-pages` branch へプッシュしており、そのとき `CNAME` ファイルが消えてしまって管理画面でカスタムドメインを設定し直さないとブログが見れなかった。

## 解決策

GitHub Actions で `gh-pages` への push は [`peaceiris/actions-gh-pages`](https://github.com/peaceiris/actions-gh-pages) を使っている。  
README をよく読んでいたら [Add CNAME file](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-add-cname-file-cname) というセクションがあった。 yml に以下を追記すれば解決できた。

```yml
- name: Deploy
    uses: peaceiris/actions-gh-pages@v3
    with:
    deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
    publish_branch: gh-pages
    publish_dir: ./docs
    // 以下を追記
    cname: blog.gennei.coffee
```

これで CI のたびにGitHubの管理画面で CNMAE を設定し直さなくてよくなった。無事解決。