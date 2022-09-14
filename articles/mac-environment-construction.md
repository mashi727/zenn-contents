---
title: "Macの環境構築あれこれ"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["macOS","Mac","環境構築"]
published: false
---


# これは何？

Macのクリーンインストールから生活環境の構築を行うまでの私的メモです。
Macでは以下に記載したとおり、さまざまなことを行っております。


# 動作環境

ハードウェアとOSまわりの環境を参考まで記載します。

- HW & OS
  - MacBook Pro (13-inch, 2019, Four Thunderbolt 3 ports)
  - macOS Monterey 12.5.1（21G83）
  - 2.8 GHz クアッドコアIntel Core i7




# ツール関係

## github

### brewにてgitのインストール

#### Montereyのクリーンインストール後の`git`

Montereyをインストールして、Command line toolsをインストールすると、`/usr/bin/`にgitもインストールされます。
2022.5.21現在で、以下のバージョンでした。

```sh
$ git --version
git version 2.32.1 (Apple Git-133)
```

新しいバージョンの方が良いので、このgitとは別にHomebrewからインストールすることにします。
`brew`からインストールされるバージョンを確認してみると

```sh
$ brew info git
git: stable 2.36.1 (bottled), HEAD
(以下略)
```

でしたので、こちらをインストールします。

```sh
$ brew install git
```

brewでインストールされる先は、`/usr/local/bin`なので、以下のようにaliasにて対応します。

```.zshrc
alias git='/usr/local/bin/git'
```
問題なく設定できていれば、以下のようになります。

```sh
$ which git
git: aliased to /usr/local/bin/git
```



## docker



# ターミナルまわり

## フォントのインストール

### [白源](https://github.com/yuru7/HackGen)


[白源 (はくげん／HackGen) ](https://github.com/yuru7/HackGen)は、プログラミング向け英文フォント Hack と、源ノ角ゴシックの派生フォント源柔ゴシックを合成して、更なる視認性向上がはかられたプログラミングフォントです。

![](/images/2022-09-14-07-18-31.png)

`brew tap`を使用してインストールを行います。
`brew tap`コマンドは、Homebrewが追跡、更新、インストールを行う公式のリストに、さらに多くのリポジトリを追加します。
詳細については、[こちら](https://github.com/Homebrew/brew/blob/master/docs/Taps.md)

インストールは、以下のコマンドで行います。

```sh
% brew tap homebrew/cask-fonts
% brew install font-hackgen-nerd
```

白源には、文字幅比率「半角1:全角2」の通常版と、通常版の白源の文字幅比率を「半角3:全角5」にしたHackGen35フォントファミリーが含まれます。HackGen35は、英数字が通常版の白源よりも大きく表示されるため、日本語が少ない文書やコードの場合にはこちらの方が読みやすいかも、とあります。

nerdの含まれたバージョンをインストールしています。
Nerd Fontsは、グリフ（アイコン）の数が多い開発者向けフォントのパッチで、Font Awesome、Devicons、Octiconsなどの人気のある「アイコンフォント」のグリフが追加されています。
Nerdの詳細については、[こちら](https://www.nerdfonts.com)を確認ください。



