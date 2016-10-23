---
title: Reactのイベントコールバックで非同期にeventオブジェクトを参照する
date: 2016-10-12 19:30 JST
tags: React
description: Reactのイベントコールバックにおいて、非同期的にeventオブジェクトを参照しようとするとエラーが出て怒られる。その原因と解決法について。
published: true
---

Reactでイベントコールバックの`event`オブジェクトに非同期でアクセスしようとするとエラーが出て怒られる｡例えば､`setState`の第二引数のコールバック内で`event`を参照しようとした時とか｡

<pre class="language-javascript"><code>function clickHandler(event) {
  this.setState({
    foo: 'bar'
  }, function () {
    console.log(event.target.value); // error
  });
}</code>
</pre>

## なぜ参照できないか

Reactのドキュメントの[Event pooling](https://facebook.github.io/react/docs/events.html#event-pooling)に書いてあった｡

> The SyntheticEvent is pooled. This means that the SyntheticEvent object will be reused and all properties will be nullified after the event callback has been invoked. This is for performance reasons. As such, you cannot access the event in an asynchronous way.

簡単に説明すると､`event`オブジェクトはReactによって`SyntheticEvent`オブジェクトとしてラップされていて､パフォーマンスのために使いまわしてますよ｡その関係で､イベントコールバックが実行され終わったら全てのプロパティを`null`にするから､非同期ではアクセスできませんよ､ということらしい｡

## 解決策

これもドキュメントの同じ項に書かれている｡

> If you want to access the event properties in an asynchronous way, you should call event.persist() on the event, which will remove the synthetic event from the pool and allow references to the event to be retained by user code.

非同期でアクセスしたかったら`event.persist()`使ってくれ､と書かれている｡なのでイベントコールバックの中で呼んであげれば非同期で`event`を参照することができる｡

<pre class="language-javascript"><code>function clickHandler(event) {
  event.persist();

  this.setState({
    foo: 'bar'
  }, function () {
    console.log(event.target.value); // ok
  });
}</code>
</pre>
