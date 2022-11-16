---
title: "PHP: pcntl_fork で生成した子プロセスの終了コードを取得する"
date: 2022-11-16T23:14:43+09:00
draft: false
tags: ["php"]
---

[pcntl_fork](https://www.php.net/manual/ja/function.pcntl-fork.php) を使っていて、`exit(100);` としたのに終了コードが 0 と出力される現象があった。とても困った && 検索しても解決策を見つけることができなかったので記事にしておく。

## ダメだったコード

[pcntl_fork](https://www.php.net/manual/ja/function.pcntl-fork.php) にあるサンプルコードに exit を追加した

```php
<?php

$pid = pcntl_fork();
if ($pid == -1) {
     die('fork できません');
} else if ($pid) {
     // 親プロセスの場合
     pcntl_wait($status); // ゾンビプロセスから守る
} else {
     // 子プロセスの場合
     exit(100);
}
```

```sh
$ php a.php
$ echo $?
0 # ここで100が表示されることを期待していた
```


## 期待した通りに動いたコード

```diff
<?php

$pid = pcntl_fork();
if ($pid == -1) {
     die('fork できません');
} else if ($pid) {
     // 親プロセスの場合
     pcntl_wait($status); // ゾンビプロセスから守る
} else {
     // 子プロセスの場合
     exit(100);
}

+ pcntl_waitpid($status);
+ exit(pcntl_wexitstatus($status));
```

```sh
$ php a.php
$ echo $?
100
```

こうしてやることで子プロセスで exit したときの終了コードを取得することができた。

---

## 思考の振り返り

結局とにかくちゃんと公式のドキュメントを読むのが一番いいことがわかった。

[PHP: pcntl_wait - Manual](https://www.php.net/manual/ja/function.pcntl-wait.php)

> pcntl_wait() は、パラメータ status の中にステータス情報を保存します。 このステータスは、次の関数を用いて評価可能です。 pcntl_wifexited()、 pcntl_wifstopped()、 pcntl_wifsignaled()、 pcntl_wexitstatus()、 pcntl_wtermsig() および pcntl_wstopsig() 。

なるほど? `pcntl_wexitstatus()` こいつ exit status と書いてあるしこれぽいな。 

[PHP: pcntl_wexitstatus - Manual](https://www.php.net/manual/ja/function.pcntl-wexitstatus.php)

> pcntl_wexitstatus — 終了した子プロセスのリターンコードを返す

引数の説明を読む。
> パラメータ status は、 pcntl_waitpid() が正常にコールされた際に得られます。

なるほど！ということは `pcntl_waitpid()` をコールしてその後に `pcntl_wexitstatus()` をコールするとよさそうだ。

---

正しいコードなのか若干怪しい気がするので詳しい人が読んだら指摘してもらいたいなあ。