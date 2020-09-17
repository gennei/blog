---
title: "macOS Catalina アップデートでエラーが発生してそれを解決した"
date: 2020-09-17T09:00:00+09:00
draft: false
tags: ["macOS", "Catalina", "ウイルスバスター"]
---

先日ずっとアップデートを先延ばしにしていた macOS アップデートをした。

Mojave -> Catalina にアップデートをしたかったのだが 8GB もある OS をダウンロードしてインストールを実行するとエラーが発生した

<a href="/img/update-error.png" target="_blank" openrel="noreferer"><img src="/img/update-error.png" width=100% alt="「パッケージ「RecoveryHDMetaDmg.pkg」からスクリプトを実行中にエラーが発生しました」"></a>

> 「パッケージ「RecoveryHDMetaDmg.pkg」からスクリプトを実行中にエラーが発生しました」

原因がわからず何度も 8GB あるファイルのダウンロードを繰り返したいが調べていたらこんな情報が。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">macOS Catalinaにアップデートしようとしたら、パッケージ「RecoveryHDMetaDmg.pkg」からスクリプトを実行中にエラーが発生しましたと表示されて失敗したけど、ウイルスバスターを無効にしたらアップデートできた。</p>&mdash; blue2718 (@blue2718281828) <a href="https://twitter.com/blue2718281828/status/1212413402103992322?ref_src=twsrc%5Etfw">January 1, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<br>
なるほどと思いウイルスバスターをオフにしたかったのだが、仕事用の PC のため権限がなく、停止することができなかった。

詰んだと思ったがいろいろと調べた結果、セーフモードで起動すればアップデートできるという情報をみつけた。

[Mac をセーフモードで起動する - Apple サポート](https://support.apple.com/ja-jp/guide/mac-help/mh21245/mac)

ということで、無事セーフモードで起動したら macOS のアップデートが完了した。 :tada:

ウイルスバスター本当に悪さしかしないなあ…。
