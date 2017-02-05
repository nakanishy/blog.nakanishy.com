---
title: webpack v1 から v2 への移行メモ
date: 2017-02-05 00:00 JST
tags: Webpack
description: 先日、webpack 2.2 の RC が取れ、[正式にリリース](https://medium.com/webpack/webpack-2-2-the-final-release-76c3d43bf144#.2v0dw9q4u)された。webpack v1 から v2 へのアップデートで幾つか Breaking changes があるため、`webpack.config.js` を変更する必要がある。移行する際のメモを残す。
eyecatch: migrating-to-webpack-2/webpack.png
published: true
---

先日、webpack 2.2 の RC が取れ、[正式にリリース](https://medium.com/webpack/webpack-2-2-the-final-release-76c3d43bf144#.2v0dw9q4u)された。
webpack v1 から v2 へのアップデートで幾つか Breaking changes があるため、`webpack.config.js` を変更する必要がある。
移行する際のメモとして残すが、詳しくは公式ドキュメントの [Migrating from v1 to v2](https://webpack.js.org/guides/migrating/) を参照のこと。

[![webpack 2](migrating-to-webpack-2/webpack.png 'webpack 2')](/images/migrating-to-webpack-2/webpack.png)

## プロパティ名の変更

config のプロパティ名が変更された。

- `module.loaders` → `module.rules`
- `loaders` → `use`
- `query` → `options`

v2 では以下のようになる。

<pre class="language-javascript"><code>module: {
  rules: [
    {
      test: /\.css$/,
      use: [
        {
          loader: "style-loader"
        },
        {
          loader: "css-loader",
          options: {
            modules: true
          }
        }
      ]
    }
  ]
}</code></pre>

## loader のチェイン

v1 では、loader を `!` で繋げることで loader から loader へ結果の受け渡しを行っていたが、
v2 の `use` では、それが廃止され、loader の Array を指定する必要がある。

<pre class="language-javascript"><code>module: {
  loaders: [
    {
      test: /\.css$/,
      loader: "style-loader!css-loader!less-loader"
    }
  ]
}</code></pre>

v2 では Array で渡す必要がある。

<pre class="language-javascript"><code>module: {
  loaders: [
    {
      test: /\.css$/,
      use: ["style-loader", "css-loader", "less-loader"]
    }
  ]
}</code></pre>

## resolve.extensions の空文字列が不要に

空文字列を指定する必要がなくなった。

このオプションは、`require` した際に省略したい拡張子を Array で指定するためもので、
`Foo.jsx` を `require("Foo")` としたい場合に `.jsx` を追加すれば良い。
逆に、拡張子付きで `require("bar.json")` のようにしたい場合は、v1 では空文字列を指定する必要があった。

<pre class="language-javascript"><code>resolve: {
  extensions: ["", ".js", ".jsx"]
}</code></pre>

v2 では空文字列を指定しなくても拡張子付きのファイルを読み込むことができる。

<pre class="language-javascript"><code>resolve: {
  extensions: [".js", ".jsx"]
}</code></pre>

拡張子が無いファイルをはじきたければ、[resolve.enforceExtension](https://webpack.js.org/configuration/resolve/#resolve-enforceextension<Paste>) を使う。

## -loader の省略が不可に

loader を指定する際、`-loader` の省略がデフォルトでは不可になった。
なおも省略したい場合は、以下のオプションを指定する必要がある。

<pre class="language-javascript"><code>resolveLoader: {
  moduleExtensions: ["-loader"]
}</code></pre>


## json-loader の指定が不要に

JSON ファイルに対する loader の指定がない場合、デフォルトで `json-loader` を使うようになった。


## UglifyJsPlugin の sourceMap がデフォルトで無効に

`UglifyJsPlugin` の `sourceMap` オプションが、従来はデフォルトで `true` だったが、v2 ではデフォルトで `false` になっている。
デバッグの際に minify される前のコード行数が知りたい場合は、明示的に `sourceMap: true` を指定する必要がある。

<pre class="language-javascript"><code>devtool: "source-map",
  plugins: [
    new UglifyJsPlugin({
      sourceMap: true
    });
  ]
}</code></pre>

