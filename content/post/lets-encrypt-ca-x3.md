---
title: "Let's Encrypt の ルート証明書の期限切れに対応する"
date: 2021-10-01T14:48:04+09:00
draft: false
tags: ["CentOS", "certificates"]
---

仕事で使用しているCentOS7のサーバーで https のリクエストがエラーになったので調査をしたら下記の記事を見つけた。

[数百万のHTTPSサイトが利用するLet’s Encryptのルート証明書が期限切れ間近、古いデバイスやOSは要注意 | TechCrunch Japan](https://jp.techcrunch.com/2021/09/25/2021-09-21-lets-encrypt-root-expiry/)

## サーバーにはいって確認

```sh
$ trust list | grep -C3 'DST Root CA X3'

pkcs11:id=%a8%e3%02%96%70%a6%8b%57%eb%ec%ef%cc%29%4e%91%74%9a%d4%92%38;object-type=cert
    type: certificate
    label: DST Root CA X3
    trust: anchor
    category: authority
```

これが問題のもの。これがいらないので対応する。

## ca-certificates を update

```sh
$ sudo yum update ca-certificates
.
.
依存性を解決しました

==========================================================================================================================
 Package                         アーキテクチャー       バージョン                          リポジトリー             容量
==========================================================================================================================
更新します:
 ca-certificates                 noarch                 2021.2.50-72.el7_9                  updates                 379 k
依存性関連での更新をします:
 p11-kit                         x86_64                 0.23.5-3.el7                        base                    252 k
 p11-kit-trust                   x86_64                 0.23.5-3.el7                        base                    129 k

トランザクションの要約
==========================================================================================================================
.
.
更新:
  ca-certificates.noarch 0:2021.2.50-72.el7_9

依存性を更新しました:
  p11-kit.x86_64 0:0.23.5-3.el7                            p11-kit-trust.x86_64 0:0.23.5-3.el7

完了しました!
```

update がきていたので更新した。

## 確認

```
$ trust list | grep -C3 'DST Root CA X3'
```

なにも表示されなくなった。

## 参考
- [RHEL/CentOS 7 Fix for Let’s Encrypt Change | by Dorai Ashok S A | Sep, 2021 | Dev Genius](https://blog.devgenius.io/rhel-centos-7-fix-for-lets-encrypt-change-8af2de587fe4)
- [Letsencrypt DST Root CA X3 expiration (September 30, 2021) workaround on CentOS 7.x OpenSSL 1.0.2 | Centmin Mod Community Support Forums](https://community.centminmod.com/threads/letsencrypt-dst-root-ca-x3-expiration-september-30-2021-workaround-on-centos-7-x-openssl-1-0-2.21965/#post-90256)
