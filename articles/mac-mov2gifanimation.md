---
title: "画面収録ファイル（mov）からgifを作成"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gif","mov","Mac","animation","gifアニメ"]
published: true
---

# これは何？

Qiita等へ投稿する際に、画面の様子をアップしたい時に、画面のgifをさくっと作成するためのワークフローです。

環境は、以下のとおりです。

- MacBook Pro (13-inch, 2019, Four Thunderbolt 3 ports)
- Darwin monju.local 21.2.0 Darwin Kernel Version 21.2.0
- ffmpeg: stable 4.4.1 (bottled)


# 概要

手順は、以下のとおりです。

1. 画面収録
1. ffmpegを使用して、ファイルを変換


# 画面のとりこみ

```sh
Shift + Command + 5
```

を押すと、範囲を指定して画面を収録することができます。
私の場合、保存先をデスクトップにしています。


# ffmpegにてgifの作成


保存されたファイルを、ffmpegを使って変換します。
[こちら](http://blog.pkh.me/p/21-high-quality-gif-with-ffmpeg.html)のスクリプトを拝借します。


```sh:gifenc.sh
#!/bin/bash

palette="/tmp/palette.png"

filters="fps=15,scale=640:-1:flags=lanczos"

ffmpeg -v warning -i $1 -vf "$filters,palettegen" -y $palette
ffmpeg -v warning -i $1 -i $palette -lavfi "$filters [x]; [x][1:v] paletteuse=dither=floyd_steinberg"  -y $2
```

使用方法は、以下のとおりです。
sl.movが、収録した画面のファイルです。

```sh
$ chmod +x gifenc.sh
$ ./gifenc.sh sl.mov sl.gif
```

以下の、gifが作成されました。


![](/images/sl.gif)
