---
title: Web MIDI API を使って電子ピアノの音名をブラウザに表示する
date: 2016-12-11 01:00 JST
tags: Piano, MIDI
description: Web MIDI API を使い、MIDI デバイスの取得から音名を表示するまで
published: true
---

最近、趣味のピアノ熱が上がっている。
理由は、USB で電子ピアノを PC に接続し GarageBand から録音できることを覚え、
日々の練習成果を残しておけるようになったため。それに伴い、エンジニア的な勉強が疎かになっていたので、
なんとかピアノ要素をこちらに持ってきたいと思い、やってみた。

## Web MIDI API について

[Web MIDI API](https://www.w3.org/TR/webmidi/) は、MIDI (Musical Instrument Digital Interface) プロトコルを
使用し、シンセサイザーやキーボード、ドラム等のMIDIデバイスにアクセスするための API 。
MIDI とは、電子楽器と PC 等の機器間で通信するための規格。

> MIDI（ミディ、Musical Instrument Digital Interface）は、日本のMIDI規格協議会（JMSC、現在の社団法人音楽電子事業協会）と国際団体のMIDI Manufacturers Association (MMA) により策定された、電子楽器の演奏データを機器間でデジタル転送するための世界共通規格。物理的な送受信回路・インタフェース、通信プロトコル、ファイルフォーマットなど複数の規定からなる。<br>
> [MIDI - Wikipedia](https://ja.wikipedia.org/wiki/MIDI)


## MIDI デバイスへのアクセス

まずは、 `navigator.requestMIDIAccess` を用い、MIDI デバイスへのアクセスを試みる。
現在 (2016年12月) 、Chrome と Opera の最新版でしか[実装されていない](http://caniuse.com/#feat=midi)ので注意。
[Polyfill](http://cwilso.github.io/WebMIDIAPIShim/) もあるが、今回はお遊びなので Chrome のネイティブ実装を使った。

<pre class="language-javascript"><code>if (navigator.requestMIDIAccess) {
  navigator.requestMIDIAccess({ sysex: false })
    .then(onMIDISuccess, onMIDIFailure);
}
</code></pre>

`requestMIDIAccess()` を実行すると Promise が返ってくる。
`then()` の第一引数に成功時のコールバック、第二引数にエラーハンドリング用のコールバックを指定。
ちなみに、`sysex` は、各電子楽器固有の機能を使用するかどうかのフラグ。今回はそこまで踏み込まないので使わない。

### アクセス成功時の処理

MIDI デバイスへのアクセスが成功したら、デバイス情報を取得し MIDI メッセージを受信したときの
イベントハンドラを設定する。

<pre class="language-javascript"><code>function onMIDISuccess(midiSuccess) {
  let devices = [];

  // MIDIの入力デバイスを取得
  let inputs = midiAccess.inputs.values();
  for (let input = inputs.next(); !input.done; input = inputs.next()) {
    devices.push(input.value);
  }

  devices.forEach((device) => {
    // 入力デバイスから MIDIメッセージを受信したときのイベントハンドラを設定
    device.onmidimessage = onMIDIMessage;
  });
}
</code></pre>

ここで注意したいのは、変数 `inputs` が [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators) である点。
`next()` 次の値を取得するためのメソッド、`done` プロパティは値を取り出し終えたかどうかの真偽値、`value` プロパティがイテレータから取り出した値である。
つまり、この `for` 文では、`inputs.next()` を `input` に代入しながら、`input.done` が `true` になるまで `devices` 配列に入力デバイスを格納している。

入力デバイスを取得後、`onmidimessage` イベントハンドラによりデバイスからのメッセージを受け取る。

<pre class="language-javascript"><code>function onMIDIMessage(message) {
  console.log(message.data);
}
</code></pre>


## MIDI メッセージのデコード

これまでのコードを実行し、電子ピアノでキーを押すと `[144, 84, 50]`のような値 、指を離すと `[128, 84, 64]` のような、先と違った値が表示される。
これはそれぞれ、`[イベントの種類, どのキーが押されたか, 音の強弱]` を表している。

一つ目の値はイベントの種類。この場合、144 は `noteOn` メッセージにあたり、キーが押されたことを示す。
128 は `noteOff` メッセージで、キーが離されたことを示す。

二つ目の値は、どのキーが押されたかを示す値。キーボードの各キーに 0 から127 までの数字が割り当てられており、どのキーが押されたか一意に特定できる。
数字と音との関係はこちらの[テーブル](http://www.midimountain.com/midi/midi_note_numbers.html)で確認できる。今回の場合、84 なので `C` にあたる。

三つ目の値は、音の強弱を表す値。これも 0 から 127 の値を取り、大きいほど強く打鍵されたことを示す。

## 音程の表示

上記より、二つ目の値がどのキーが押されたかを示していることはわかったが、数値のままでは理解しづらいため、
数値から音名に変換するメソッドを定義する。1オクターブ中に含まれる鍵盤の数は12キーあるため、12 の剰余で求められる。

<pre class="language-javascript"><code>const NOTES = [
  'C', 'C#', 'D', 'D#', 'E', 'F',
  'F#', 'G', 'G#', 'A', 'A#', 'B'
];

function getNoteName(number) {
  return NOTES[number % 12];
}
</code></pre>

## まとめ

意外と簡単に MIDI デバイスからのデータを取得できた。
最近趣味の時間がピアノに取られがちでエンジニア的な勉強が疎かになっていたので、
こういう形で趣味とエンジニアリングを繋げられるのは良い。
ここから人に見せられるくらいのプロダクトを作っていきたい。
