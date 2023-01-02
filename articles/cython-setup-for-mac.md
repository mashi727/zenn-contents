---
title: "Mac de Cython 〜Mandelbrot集合の高速化(1)〜"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Mac","Cython","OpenMP"]
published: false
---

# これは何？


CythonをMacで動かすまでの記事です。

## 経緯

Software Design 2022年12月号のCythonの記事に、Mandelbrot集合の高速化の例が載っていたので、かねてより作成していた表示プログラム（下図）への適用を試みた記録です。

![](/images/2022-12-08-18-40-03.png)

## 記事の範囲

3回に分けて実装までを紹介します。
1回目はCythonを動かすところまで、2回目の記事では構想検討を、3回目の記事で実装を行う予定です。

# 行った手順（結論）

結果としてうまくいった手順は、以下のとおりです。

1. gccのインストール
1. 




```sh
% which gcc
/usr/bin/gcc
% cd /usr/bin
% ./clang--version
Apple clang version 14.0.0 (clang-1400.0.29.202)
Target: x86_64-apple-darwin22.1.0
Thread model: posix
InstalledDir: /Library/Developer/CommandLineTools/usr/bin
mashi@monju(8:07:06) /usr/bin
```

OpenMPを入れないと動かなかったので






## 動作環境

当方の環境は、以下のとおりです。

- HW
  - MacBook Pro (13-inch, 2019, Four Thunderbolt 3 ports)
  - macOS Monterey 12.5.1（21G83）
  - 2.8 GHz クアッドコアIntel Core i7
- SW
  - Python 3.10.4
  - PySide6 6.3.1
  - PyQtGraph


