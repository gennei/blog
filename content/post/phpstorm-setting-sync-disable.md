---
title: "PhpStorm の設定のExportができなくなったのを対応する"
date: 2020-06-13T14:51:05+09:00
draft: false
tags: ["phpstorm"]
---

仕事で PhpStorm を使っている。設定を共有するのに GitHub の repository 経由で共有していた。ただ、別の人が GitHub の repository を指定したがうまくいかなかった。しょうがないので設定をエクスポートして、取り込んでもらおうとしたら設定のエクスポートができなくなっていた。

<!-- more -->

### repository 設定をしているときのメニュー

<a href="/img/phpstorm-invisible-export.png" target="_blank" rel="noopener">
  <img src="/img/phpstorm-invisible-export.png" width=100%>
</a>

### 解決方法としてはプラグインを disable にする

<a href="/img/plugin-setting-repository-disable.png" target="_blank" rel="noopener">
  <img src="/img/plugin-setting-repository-disable.png" width=100%>
</a>

### disable にしたあとのメニュー

<a href="/img/phpstorm-visible-export.png" target="_blank" rel="noopener">
  <img src="/img/phpstorm-visible-export.png" width=100%>
</a>

これわかりにくいのどうにかならないかな。Export できなくなっているのは一理あるけども。
