---
title: Mastodon（マストドン）の空いているユーザー名を一括検索
date: 2017-04-18 00:00 JST
tags: Release
description: 空きアカウント検索サービス「Availy」を Mastodon に対応させました。
eyecatch: availy-for-mastodon/availy.png
published: true
---

Mastodon が巷で話題なので、流行りに乗って [Availy でも対応](https://availy.me/mastodon)してみた。

[![Availy](/images/availy-for-mastodon/availy.png 'Availy')](https://availy.me/mastodon)

## Availy について

[Availy](https://availy.me) は、複数の SNS や Web サービスをまたがって空きアカウントの検索ができるサービス。
Twitter や Instagram をはじめとする SNS を扱っていたが、Mastodon にも対応。
たくさんある Mastodon サーバーから、ユーザー名が空いているかどうかを一括検索することができる。

技術的にやってることは以前と変わらず、HEAD リクエストを投げ、レスポンスのステータスコードから空き状況の判定を行う。
技術的な詳細は、[リリース記事](https://blog.nakanishy.com/launch-availy.html) に書いてあるので、興味のある方はどうぞ。

## 対応サーバー

小さなサーバーもすべて含めるときりがないため、[Mastodon instances](https://instances.mastodon.xyz/list) に登録されているユーザー数が多いサーバー上位40件を取ってきている。
海外のサーバーも多いのでそこははじくか検討中。

## Mastodon のユーザー名について

Mastodon のユーザー名は、Twitter のように世界に一つではなく、各サーバー毎に同じ名前を取得することができる。
例えば、Pixiv が運営している Pawoo で @nakanishy が取れなくても、 mstdn.jp で @nakanishy が空いていれば取れると言った具合に。

また、Mastodon には「リモートフォロー」という機能があり、別サーバー上のユーザーをフォローすることができる。
その際、ユーザーを表す ID として、__@ユーザー名@サーバー名__ で表される。
したがって、mstdn.jp 上で @nakanishy というユーザー名を取った場合 __@nakanishy@mstdn.jp__ となる。

焦ってユーザー名を取る必要はないが、有名なサーバー上でアカウントが欲しいのであれば早めにしたほうが良いだろう。
