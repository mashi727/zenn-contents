---
title: "PyQtGraphを用いたファイナンスデータの可視化（1）"
emoji: "💰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python","PyQtGraph","Fintech"]
published: true
---
# これは何？

ファイナンスのデータをダウンロードして表示するアプリケーションの制作記録です。作成するアプリケーションのイメージは以下のとおりです。

![](/images/fintech.gif)

これも少し前に作成したものなので、バックテストなどの機能を盛り込みすぎてかえって使いづらくなってしまいました。

ということで、今回は機能をブラッシュアップしつつ取り組もうと考えておりますので見た目などが異なります。

公開しながら行うことにより、作業の進みが早くなることを期待しております。ということで、少しずつ書き足しながら、かつシリーズものとして作成を行います。

## 環境

当方の環境は以下のとおりです。

- HW
  - MacBook Pro (13-inch, 2019, Four Thunderbolt 3 ports)
  - macOS Monterey 12.5.1（21G83）
  - 2.8 GHz クアッドコアIntel Core i7
- SW
  - Python 3.10.4
  - PySide6 6.3.2
  - pyqtgraph 0.13.1


2880x1800のdot by dotにて使用しているため、他の解像度の環境ではフォントの表示が大きくなりすぎることもあろうかと思います。
あらかじめ、ご承知ください。


## 可視化（1）の範囲

可視化（1）では、

Yahoo Financeデータをもとに、ファンダメンタ分析に関する基本的な指標の計算と可視化を行います。

- チャートの表示
- 財務諸表の取得、表示
- 企業価値（PSR）の計算
- 高成長企業の評価（40%ルール）
- 複数の企業の比較（データの取得と可視化）
- 株価の成長率の評価

これらの指標は、証券会社のHP、YahooやGoogleなどのサイトでも確認することができます。一方で、複数の企業を同時に比較することや、論文に記載されたアルゴリズムを試すには制限があります。

自作のソフトを作成する理由は、まさにここにあると考えています。

ということで、ぼちぼちと地道に作成を進めたいと思います。

# ユーザー・インターフェイス（UI）の作成

まずはdesignerにて、UIをサクッと作成します。

起動するコマンドは、`pyside6-designer`になります。
ちゃちゃっとウィジェットの配置を行いまして、以下のUiを作成しました。

![](/images/2022-10-01-19-11-47.png)

とりあえずは、こんな感じをスタートとします。
作成をすすめる中で修正することもあるでしょうが、はじめから完璧に作成しようとすると制作が進まないので、とりあえずこのUIで進めようと思います。

使用しているウィジェットを簡単に説明します。

正面は、QGraphicsViewをベースクラスとしてdockareaを格上げしたウィジェットです。この中に、Dockを作成してグラフを追加していきます。

Dockは。チャート表示用とregion表示用の2つを作成しています。


![](/images/2022-10-01-19-16-32.png)


両サイドと、下はQTableViewを使用しています。左サイドは株の銘柄の選択用で、右サイドは財務諸表などを表示するつもりです。下のテーブルは、データフレームの表示用です。


# 銘柄の検索と表示


tableViewまわりの設定を行います。


```sh
% pyside6-designer financeUi.ui
```
designerにてuiファイルを起動し、左側の表示させたいtableView（この場合はtableView_ticker_symbols）をクリックした状態で、右側のプロパティエディタにて、

![](/images/2022-10-02-09-41-02.png)

selectionModeを、ExtendedSelectionにします。
このことにより、以下のように行単位で選択できるようになります。

![](/images/2022-10-02-08-53-56.png)

垂直（Vertical）方向のHeaderの削除は、commandsフォルダにあるmake_tableview_mode.pyに定義されている、TableModelにて行います。

```python
    def headerData(self, section, orientation, role):
        if role == Qt.DisplayRole:
            if orientation == Qt.Horizontal:
                return str(self._data.columns[section])

            if orientation == Qt.Vertical:
                return str(self._data.index[section])
        return ""  # There is no vertical header
```

最終行にある、`return ""`が該当するコードになります。
これを設定することにより、上記の表示が以下の表示になります。

![](/images/2022-10-02-09-03-27.png)


起動後に検索する様子は、以下のとおりです。
データの取得を行う場合は、少し時間がかかりますが二度目からは気にするほどの時間はかからないものと思います。

![](/images/symbol_search.gif)

次回は、株価データの取得とチャートの表示を行います。

# 株価データの取得

# チャートの表示





# 現状のコード

`financeCall.py`を実行すると、アプリが起動します。

クローンしてお試しください。

https://github.com/mashi727/finance_basic