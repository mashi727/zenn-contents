---
title: "PyQtGraphの凡例などのフォント設定について"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["PyQtGraph","python","PySide6","Legend"]
published: true
---
# これは何？


PyQtGraphでグラフを描く際の凡例の追加に関するメモです。
完成イメージは、以下のとおりです。

![](/images/Lissa.gif)


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

# 要領

## x軸、y軸やグリッドなどの設定

グラフのグリッドなどは、lambda関数を用いて設定します。
ここで、x軸、y軸名なども設定してしまいます。`Legend(凡例)`の設定もここで行います。


```python
setprop = lambda x: (x.setAutoVisible(y=True),
                     x.enableAutoRange(False),
                     x.showAxis('right'),
                     x.showAxis('left'),
                     x.showAxis('top'),
                     x.getAxis('top').setHeight(50),
                     x.getAxis('bottom').setHeight(50),
                     x.getAxis('right').setWidth(50),
                     x.getAxis('left').setWidth(50),
                     x.showGrid(x=True, y=True, alpha = 1),
                     x.setAutoVisible(y=True),
                     x.setXRange(-1, 1, padding=0.1),
                     x.setYRange(-1, 1, padding=0.1),
                     x.addLegend(offset=(10,10)),
                     x.setTitle('<font size=\'14\' color=\'#FFFFFF\'>'+ 'Lissajous' +'</font>'),
                     x.setLabel('left'  , text='y', units='', **styles),
                     x.setLabel('bottom', text='x', units='', **styles),
                     x.setLabel('right'  , text='y', units='', **styles),
                     x.setLabel('top', text='x', units='', **styles),
                     )
```

## フォント名の指定

フォントの指定も、一括で行います。

```python
self.fontCssLegend = '<style type="text/css"> p {font-family: Helvetica, HackGen35 Console NFJ; font-size: 15pt; color: "#ffffff"} </style>'
styles = {'color':'white',
          'font-size':'30px',
          'font-style':'bold',
          'font-family': 'Helvetica, HackGen35 Console NFJ'
          }
```

Macの場合、フォント名は、Font Bookの正式名称の下にある「ファミリー」を使用します。

![](/images/2022-10-01-17-21-05.png)

ヒラギノフォントなどを指定すると、ときどき以下のようなメッセージが出力されることがあります。

```sh
qt.qpa.fonts: Populating font family aliases took 512 ms. Replace uses of "ヒラギノ角ゴシック" with its non-localized name "Hiragino Sans" to avoid this cost.
```

このような場合、あわてずさわがず指示通りにHiragino Sansを設定すれば問題ありません。


lissajousCall.pyのファイルです。

https://github.com/mashi727/lissajous/blob/main/lissajousCall.py

plotsingleUi2.pyのファイルです。

https://github.com/mashi727/lissajous/blob/main/plotsingleUi.py


githubのリポジトリは、こちらです。

https://github.com/mashi727/lissajous