---
title: "foliumを用いて地図表示"
emoji: "🗺️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["folium","python","PySide6","Qt"]
published: false
---

# これは何？

foliumを用いて地図を表示するテストピースです。
完成イメージは、以下のとおりです。


![](/images/2022-09-09-22-32-11.png)

左上はQWebEngineViewで、foliumのmapオブジェクトを表示しています。
foliumのmapオブジェクトは、右下のQPlainTextEditに記述します。通常 m = で記載する部分をコピペしてPlotボタンを押すと描画されます。

foliumのmapオブジェクトは、Saveボタンでテキストとして保存することができます。
拡張子は、folium objectを表すfoとしました。

右上は、QTreeViewにカレントディレクトリにあるfo拡張子（folium object）のファイルを表示しています。クリックすると右下の画面に中身が表示されます。

# 動作環境

- HW
  - MacBook Pro (13-inch, 2019, Four Thunderbolt 3 ports)
  - macOS Monterey 12.5.1（21G83）
  - 2.8 GHz クアッドコアIntel Core i7
- SW
  - Python 3.10.4
  - PySide6 6.3.1
  - folium 0.12.1.post1

# 作成の経緯


休日に時間があると自転車に乗っています。遠出をすることも多いのでルートを検索して行程を決めるのですが、その際に検索したルートの標高や斜度を調べる良いツールをなかなか見つけられずにおります。

Pythonで地図を作成したことがなかったためこれを機につくってみようと思いたったのがきっかけです。アプリは、以下のような感じで作ろうと考えておりまして

1. PySide6のウィジェットに地図を埋め込み
1. ルート検索は、OpenrouteserviceもしくはGoogle My Mapsで検索をしてkmlエクスポートから読み込み
1. 斜度などを計算してPyQtGraphにて可視化

この記事では、その中のPySide6に地図を埋め込むところまでになります。


# 制作の手順

前提となるPySide6の環境構築などは、わたしもメモがてらまとめておりますが、世の中には良い記事がたくさんありますので、ご自身のやりたいことにあわせて適宜ご参照ください。


- Pythonの環境構築に関しては、[こちら]()
- PySide6のインストールに関しては、[こちら]()



