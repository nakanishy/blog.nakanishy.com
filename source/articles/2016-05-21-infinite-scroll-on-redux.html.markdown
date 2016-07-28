---
title: Reduxで無限スクロールを作る
date: 2016-05-21 15:59 JST
tags: React, Redux, JavaScript
published: false
---

Reduxを使った無限スクロールの実装です。

## 無限スクロールとは

スクロールダウンしたとき自動的に次のコンテンツを読みに行く更新スタイルのことを言います。英語ではInfinite Scroll。

<pre class="language-javascript"><code>var p = new Promise(function(resolve, reject) {
  // Do an async task async task and then...

  if(/* good condition */) {
    resolve('Success!');
  }
  else {
    reject('Failure!');
  }
});

p.then(function() {
  /* do something with the result */
}).catch(function() {
  /* error :( */
})</code>
</pre>

こんにちは
こんばんは

## Reduxでの実装

ES6の`class`構文を使っていくスタイルで書いていく。

<pre><code class="language-javascript">class InfiniteScroll extends React.Components {
  constructor(props) {
    super(props)
    this.handleScroll = this.handleScroll.bind(this)
  }
  componentDidMount() {
    window.addEventListener('scroll', handleScroll)
  }
  componentDidUnmount() {
    window.removeListener('scroll', handleScroll)
  }
  handleScroll(e) {
    // do something
  }
}</code>
</pre>

ちなみにcssのコードはこちら

これはむずかしい

## aabc

なぜ羽井さえr内？

<pre class="language-css"><code>html {
  margin: 0;
  padding: 0;
}

.header {
  width: 100;
  background: #ddd;
}
</code></pre>

実行するコマンドは以下のようになります。この通りiTermに入力してください

<pre class="language-bash">
<code># Remove the machine, confirming "y" when asked by docker-machine
echo 'y' | docker-machine rm default
</code></pre>

最後に書かれる文字列もすべてhtmlとして扱われちゃう？
