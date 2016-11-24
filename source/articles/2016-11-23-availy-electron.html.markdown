---
title: AvailyをElectronアプリ化してみた
date: 2016-11-23 23:00 JST
tags: Electron
description: 
eyecatch: launch-availy/availy.png
published: false
---

先日リリースした[Availy](https://availy.me)をElectronアプリ化した。
別にアプリ化するメリットも特にないので、ただのお遊び。

[![Availy](/images/launch-availy/availy.png 'Availy')](/images/launch-availy/availy.png)

## インストール

必要なパッケージをインストールする。

<pre class="language-bash"><code>$ npm install --save-dev electron electron-packager</code></pre>


何も難しくない。[Events](https://github.com/electron/electron/blob/master/docs/api/app.md#events)

<pre class="language-javascript"><code>'use strict';

const electron = require('electron');
const app = electron.app;
const BrowserWindow = electron.BrowserWindow;

let mainWindow;

app.on('window-all-closed', function() {
  if (process.platform != 'darwin')
    app.quit();
});

app.on('ready', function() {
  mainWindow = new BrowserWindow({
    width: 800,
    height: 600
  });

  mainWindow.loadURL('https://availy.me');

  mainWindow.on('closed', function() {
    mainWindow = null;
  });
});</code>
</pre>

`BrowserWindow`には

## 起動
