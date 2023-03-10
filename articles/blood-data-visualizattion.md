---
title: "ラブラッドの献血記録の可視化"
emoji: "🏥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Python","PyQtGraph","献血","Selenium"]
published: false
---



# これは何？

ラブラッドのサイトから閲覧できるデータのうち、2009.3.15以降の献血データを取得するプログラムです。

![](/images/2023-03-09-07-49-37.png)

2009.3.15以降とした理由は、血液分析の項目が異なるため実装が面倒だったからです。


# 当方の環境

当方の環境は以下のとおりです。

## HW
- MacBook Pro (13-inch, 2019, Four Thunderbolt 3 ports)
- macOS Monterey 12.5.1（21G83）
- 2.8 GHz クアッドコアIntel Core i7

## SW
- Python 3.10.4
- PySide6 6.4.2
- pyqtgraph 0.13.1

2880x1800のdot by dotにて使用しているため、他の解像度の環境ではフォントの表示が大きくなりすぎることもあろうかと思います。あらかじめ、ご承知ください。

# プログラムの概要

プログラムにて行っていることの概要は、以下のとおりです。

## get_blood_data.py

実行すると、Chromeが立ち上がりログイン処理、血液の分析結果の取得などが行われ、スクリプトと同じフォルダに「blood_data.csv」が作成されます。seleniumを用いて、ラブラッドへのサイトのログインを行い献血の記録を表示、データの取得を行っています。

ログイン後は、`mod-past-data__result`には、ダミー献血日を含む献血日の情報入っているので、これをもとに、すべての献血回数（`num_of_kenketsu_all`）と、今回データ取得の対象とする2009.3.15以降の献血回数（`num_of_kenketsu`）を求めて、ページを捲る回数などを算出し、ページを捲りながらデータを取得します。

![](/images/2023-03-10-07-24-30.png)


データは、以下のコードにて表示されています。

![](/images/2023-03-10-07-21-35.png)

`mod-result-table__data`タグに入っているデータを取得します。


### *** エラー関連 ***

実行すると、

selenium.common.exceptions.StaleElementReferenceException: Message: stale element reference: element is not attached to the page document

とエラーがでる場合があります。
ページの遷移が間に合っていないと思われますので、待ち時間をより長く設定してみてください。

本来は、状態変更をみるようにするのが良いのでしょうが、現状ではそこまでは行っていません。
そのうち、取得時間の節約やスキルアップのために、明示的な待機（WebDriverWait.until()）などを組み込むかもしれません。

### その他

ラブラッドサイトの解析は、Selenium IDEを使用しました。
これがなかったら、解析の時間がもっとかかっていたと思われます。

![](/images/seleniumIDE.gif)


## csv2sqlite3.py

`csv`ファイルを読み込んで、データベースファイルを作成しています。


## dataVizCall.py

`db`ファイルをpandasのデータフレームとして読み込んで、可視化を行っています。
可視化には、`pyqtgraph`を用いています。いちいち、ブラウザを開くのが面倒だったり、matplotlibは印刷用途とのイメージがあるので、`pyqtgraph`を用いています。



# 準備

## ラブラッドにログインするための.passwordファイルの作成

`get_blood_data.py`を実行するフォルダに`.password`ファイルを以下の内容で作成します。

```sh
BLOODCODE = 'xxxxxxxxxx'
PASSWORD = '**********'
RECORDPASSWORD = '****'
```

`get_blood_data.py`を実行すると、Chromeが立ち上がりデータの取得が行われます。
問題なければ、実行したフォルダに`blood_data.csv`が作成されます。

`csv2sqlite3.py`にて、`blood_data.csv`を``blood_data.db`に変換します。
引数なしで、実行します。

そのうち、`get_blood_data.py`にて保存するファイルをSQLite3の`db`にて作成するように更新するかもしれません。


# 実行方法
1. `python get_blood_data.py`を実行します。
1. `python csv2sqlite3.py`を実行します。
1. `python dataVizCall.py`

問題なく実行されると、以下の表示を確認することができるかと思います。（再掲）


![](/images/2023-03-09-07-49-37.png)
