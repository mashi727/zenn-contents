---
title: "私的Python環境構築"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Python","pyenv"]
published: false
---

# これは何？

Pythonの環境構築を行った際の私的メモです。

dockerを使った環境なども構築して試してみましたが、結果、日常的に使う開発環境は`pyenv`にてMacのオリジナルの`Python`環境と分離し、本や雑誌などの記事を試す際の環境はdockerにて構築することとしています。

`docker compose`で作成した環境を使わないこととしたのは

- Macの環境に比べ遅いこと
- OpenGLが落ちる

などの理由が大きいと考えています。



# 環境

- HW
  - MacBook Pro (13-inch, 2019, Four Thunderbolt 3 ports)
  - macOS Monterey 12.5.1（21G83）
  - 2.8 GHz クアッドコアIntel Core i7
- SW
  - Python 3.10.4
  - PySide6 6.3.1
  - folium 0.12.1.post1