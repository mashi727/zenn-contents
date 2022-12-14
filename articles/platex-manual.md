---
title: "TeXを使って、とあるマニュアルを作成した記録"
emoji: "📓"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["upLaTeX","TeX","prh"]
published: false
---

# これは何？

ひょんなことから、とあるツールに関するインストールマニュアルを作成することにしました。

markdownで作成してpdfに変換して送付したのですが、品質的にイマイチだなと感じたので、昨今のツール事情を調べつつブラッシュアップすることにしました。以下は、いまいちだなと感じたもの。

![](/images/2022-09-26-21-09-00.png)

いまいちだと思った点は、

- 図の大きさと、文章のバランスが悪い。
- 上記のバランスとしては2段組がお好み（作成実績あり）だが、markdownでは無理そう。

図の大きさと文章のバランスは、以下のように図の幅を指定する方法で解決できそうですが、以前作成した2段組の方が好みに合う感じです。

```markdown
<p align="center"><img src='./imgs/hfugafuga.png' width='20%' style="display: block; margin: auto;"></p>
```

以前、といってもかれこれ20年前ほど前になりますが、作成したものはこちら。

![](/images/2022-09-26-21-18-46.png)
*以前作成したPDF*

当時はFreeBSDを使用しておりまして、現在でもpLaTeXの環境を整えるだけで同じ出力が得られるのも感慨深いですね。

本稿はその際のメモです。

## 本記事が伝えること

### LaTeXによるPDF出力の方法

最終的には、upLaTeXを使用してpdfにて出力しています。
完成のイメージは、以下のとおりです。

![](/images/2022-09-24-19-51-53.png)
*今回出力したPDF*

### ツールの選択の過程

ツールの使い方に関する記事はよく見かけるのですが、実現したいコトからツールを選択するまでのプロセスにスコープをあてたものはあまりみかけません。

自明だから、そんなものなくてもというご意見もあるでしょうが、コトづくりに関心をもっていることもあり、「実現したいコトから、実現する手段の選択まで」を含む記事を作成してみようと考えた次第です。

## 本記事の動作環境

- HW
  - MacBook Pro (13-inch, 2019, Four Thunderbolt 3 ports)
  - macOS Monterey 12.5.1（21G83）
  - 2.8 GHz クアッドコアIntel Core i7
- SW
  - Docker version 20.10.17, build 100c70180f (multipass上にて稼働)
  - multipass   1.10.1+mac
  - multipassd  1.10.1+mac

Dockerが動けば、Windowsでも実現可能です。


# 行ったこと（ツール選定後）

- 本ケースにおけるベストプラクティス
  - ニーズのおさらい
  - アウトプットのスタイル
  - 自分自身のニーズ
    - 図の取り込み
    - 動画の扱い
    - バージョン管理
- ツールの確定
  - 執筆環境の整備
    - 執筆構築（VS Code）
    - TeXの環境（Docker）





# ベストプラクティス

以下の順で考えました。

- ニーズをおさえアウトプットのスタイルを決める
- スタイルにそったワークフローの検討
- 

ものをかくということに関しては、さまざまな選択肢があります。まずは、使用されるケースから望ましいアウトプットのスタイルについて考えてみます。

そのスタイルは、電子的な媒体にするか印刷物にするかの2択でしょう。





まずはじめに、環境としてなにを準備すれば良いのか。


遠い昔、学生及び社会人一年生の頃に書いた教科書的な100ページほどのマニュアルは、pLaTeXを使用しておりました。

その理由は、

- 数式込みであったということ
- 目次やリファレンスを自動処理したかったこと
- 学生のころからTeXを使っていたこと
- レイアウト作成と書く行為を分離したかったこと

くらいでしょうか。

数年前に作成した仕事関係の70ページほどのマニュアルは、Wordで作成しました。

理由は

- 職場の環境的にWordと一太郎しか選択できなかったこと
- 文字と図のみで、数式が必要なかったこと
- 目次や相互参照を自動で出力できたこと

ファイルが重くなるなどいろいろと気になることはありましたが、最終的にはWordで作成したものをpdfに変換して済ませました。



改めてものをかくということに関する最近の状況は、

## 原稿をかく






# 考えたこと



![](/images/2022-09-27-11-03-19.png)

まずは、iPadで作成したメモから。

![](/images/2022-09-26-21-46-34.png)
*iPadのGoodNotes 5にて作成したラフ*

スキル的にも、時間的にも問題ないと思いましたので「Aさんが、Finale 27でNote Performer 3を使用することができるよう、マニュアルをPDFにて作成して、サポートする。」というお題に取り組むこととしました。

当初は、時間をあまりかけずにちゃちゃっと作成したいと考えまして、自身のメモ用に使っているMarkdownのノートとしてVS Codeで作りはじめました。その結果、作業時間2時間弱、出勤前の朝活の時間でさくっと作成して、送付しました。

後に見直しを行ったところ、美しくなく、読みづらく、かっこわるいなと思いましたので改めて作成したものです。

作成にあたって考えたことは、以下のとおりです。


![](/images/2022-09-24-16-08-56.png)

この図をもとに、タスクへの落とし込みを行います。



