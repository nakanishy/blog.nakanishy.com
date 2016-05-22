---
title: Reduxで無限スクロールを作る
date: 2016-05-21 15:59 JST
tags: React, Redux, JavaScript
published: false
---

Reduxを使った無限スクロールの実装です。

## 無限スクロールとは

スクロールダウンしたとき自動的に次のコンテンツを読みに行く更新スタイルのことを言います。英語ではInfinite Scroll。

## Reduxでの実装

ES6の`class`構文を使っていくスタイルで書いていく。

```javascript
class InfiniteScroll extends React.Components {
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
}
```

ちなみにcssのコードはこちら

```html
<!DOCTYPE html>
<html>
  <div class="container">
    <h1>Inf. Scroll</h1>
    <p>do something</p>
  </div>
</html>
```

```scss
html {
  margin: 0;
  padding: 0;
}

.header {
  width: 100;
  background: #ddd;
}
```

実行するコマンドは以下のようになります。この通りiTermに入力してください

```shell
# Remove the machine, confirming "y" when asked by docker-machine
echo 'y' | docker-machine rm default
```

