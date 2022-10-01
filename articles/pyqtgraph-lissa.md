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

:::details サンプルコード

lissajousCall.pyのファイルです。

```python:lissajousCall.py
import sys
import numpy as np

from PySide6.QtWidgets import QApplication, QMainWindow
from PySide6.QtCore import QTimer
from PySide6.QtGui import Qt

import pyqtgraph as pg


# 自作のライブラリ
from plotsingleUi2 import Ui_MainWindow


class MainWindow(QMainWindow, Ui_MainWindow):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.setupUi(self)
        self.p1 = self.set_graph_ui()

        self.startTimer()
        self.spinBox_x.valueChanged.connect(self.startTimer)
        self.spinBox_x.valueChanged.connect(self.graphClear)
        self.spinBox_y.valueChanged.connect(self.startTimer)
        self.spinBox_y.valueChanged.connect(self.graphClear)

        self.i = 0
        self.samplingNum = 2000


    def graphClear(self):
        self.p1.clear()

    def lissajous(self,i):
        m = self.spinBox_x.value()
        n = self.spinBox_y.value()
        t = np.linspace(-np.pi,np.pi,self.samplingNum)
        x = np.sin(m * t[i])
        y = np.sin(n * t[i])
        return x, y

    def startTimer(self):
        self.timer = QTimer()
        self.timer.timeout.connect(self.plot_xy)  # QTimer が timeout した場合に呼び出す関数を登録
        self.timer.start(1)  # タイマーをスタートさせる



    def set_graph_ui(self):
        """_summary_

        Returns:
            _type_: _description_
            軸やレジェンドなどを設定してGraphオブジェクトを返す
        """
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
        '''
        Macの場合、フォントはFont Bookの正式名称の下にある「ファミリー」を使用する。
        ときどき、non-localizedといわれる以下のメッセージが出力される。

        qt.qpa.fonts: Populating font family aliases took 512 ms. Replace uses of "ヒラギノ角ゴシック"
        with its non-localized name "Hiragino Sans" to avoid this cost.

        この場合は、指示通りにHiragino Sansを設定すれば問題はない。
        '''
        self.fontCssLegend = '<style type="text/css"> p {font-family: Helvetica, HackGen35 Console NFJ; font-size: 15pt; color: "#ffffff"} </style>'
        styles = {'color':'white',
                  'font-size':'30px',
                  'font-style':'bold',
                  'font-family': 'Helvetica, HackGen35 Console NFJ'
                  }

        p1 = self.graphicsView.addPlot()
        setprop(p1)
        return p1

    def plot_xy(self):
        if self.i < self.samplingNum-1:
            x,y = self.lissajous(self.i)
            self.p1.plot([x],[y],
                         clear=True,
                         pen='#0F0',
                         alpha=1,
                         symbolBrush='#0F0',
                         symbolSize=10,
                         name=self.fontCssLegend + '<p>Plotポイント</p>'
                         )
            self.i  += 1
        else:
            self.i = 0


def main():
    app = QApplication(sys.argv)
    window = MainWindow()
    window.show()
    app.exec()

if __name__ == '__main__':
    main()
```
:::



:::details サンプルコード

plotsingleUi2.pyのファイルです。

```python:plotsingleUi2.py
# -*- coding: utf-8 -*-

################################################################################
## Form generated from reading UI file 'plotsingleUi2.ui'
##
## Created by: Qt User Interface Compiler version 6.3.2
##
## WARNING! All changes made in this file will be lost when recompiling UI file!
################################################################################

from PySide6.QtCore import (QCoreApplication, QDate, QDateTime, QLocale,
    QMetaObject, QObject, QPoint, QRect,
    QSize, QTime, QUrl, Qt)
from PySide6.QtGui import (QBrush, QColor, QConicalGradient, QCursor,
    QFont, QFontDatabase, QGradient, QIcon,
    QImage, QKeySequence, QLinearGradient, QPainter,
    QPalette, QPixmap, QRadialGradient, QTransform)
from PySide6.QtWidgets import (QApplication, QGraphicsView, QGridLayout, QLabel,
    QMainWindow, QMenuBar, QPushButton, QSizePolicy,
    QSpacerItem, QSpinBox, QStatusBar, QWidget)

from pyqtgraph import GraphicsLayoutWidget

class Ui_MainWindow(object):
    def setupUi(self, MainWindow):
        if not MainWindow.objectName():
            MainWindow.setObjectName(u"MainWindow")
        MainWindow.resize(875, 775)
        sizePolicy = QSizePolicy(QSizePolicy.Preferred, QSizePolicy.Preferred)
        sizePolicy.setHorizontalStretch(0)
        sizePolicy.setVerticalStretch(0)
        sizePolicy.setHeightForWidth(MainWindow.sizePolicy().hasHeightForWidth())
        MainWindow.setSizePolicy(sizePolicy)
        self.centralwidget = QWidget(MainWindow)
        self.centralwidget.setObjectName(u"centralwidget")
        self.gridLayout = QGridLayout(self.centralwidget)
        self.gridLayout.setObjectName(u"gridLayout")
        self.label = QLabel(self.centralwidget)
        self.label.setObjectName(u"label")
        font = QFont()
        font.setPointSize(16)
        self.label.setFont(font)

        self.gridLayout.addWidget(self.label, 2, 1, 1, 1)

        self.label_3 = QLabel(self.centralwidget)
        self.label_3.setObjectName(u"label_3")
        self.label_3.setFont(font)

        self.gridLayout.addWidget(self.label_3, 1, 1, 1, 1)

        self.verticalSpacer = QSpacerItem(20, 40, QSizePolicy.Minimum, QSizePolicy.Expanding)

        self.gridLayout.addItem(self.verticalSpacer, 8, 2, 1, 1)

        self.pushButton = QPushButton(self.centralwidget)
        self.pushButton.setObjectName(u"pushButton")
        sizePolicy1 = QSizePolicy(QSizePolicy.Minimum, QSizePolicy.Preferred)
        sizePolicy1.setHorizontalStretch(0)
        sizePolicy1.setVerticalStretch(0)
        sizePolicy1.setHeightForWidth(self.pushButton.sizePolicy().hasHeightForWidth())
        self.pushButton.setSizePolicy(sizePolicy1)
        font1 = QFont()
        font1.setPointSize(16)
        font1.setBold(False)
        self.pushButton.setFont(font1)

        self.gridLayout.addWidget(self.pushButton, 10, 2, 1, 1)

        self.graphicsView = GraphicsLayoutWidget(self.centralwidget)
        self.graphicsView.setObjectName(u"graphicsView")
        self.graphicsView.setMinimumSize(QSize(700, 700))
        self.graphicsView.setResizeAnchor(QGraphicsView.AnchorViewCenter)

        self.gridLayout.addWidget(self.graphicsView, 0, 0, 11, 1)

        self.labelPosX = QLabel(self.centralwidget)
        self.labelPosX.setObjectName(u"labelPosX")
        sizePolicy.setHeightForWidth(self.labelPosX.sizePolicy().hasHeightForWidth())
        self.labelPosX.setSizePolicy(sizePolicy)
        font2 = QFont()
        font2.setPointSize(24)
        self.labelPosX.setFont(font2)
        self.labelPosX.setAlignment(Qt.AlignLeading|Qt.AlignLeft|Qt.AlignVCenter)

        self.gridLayout.addWidget(self.labelPosX, 5, 1, 1, 1)

        self.spinBox_x = QSpinBox(self.centralwidget)
        self.spinBox_x.setObjectName(u"spinBox_x")
        self.spinBox_x.setFont(font)
        self.spinBox_x.setAlignment(Qt.AlignCenter)
        self.spinBox_x.setValue(7)

        self.gridLayout.addWidget(self.spinBox_x, 1, 2, 1, 1)

        self.spinBox_y = QSpinBox(self.centralwidget)
        self.spinBox_y.setObjectName(u"spinBox_y")
        sizePolicy.setHeightForWidth(self.spinBox_y.sizePolicy().hasHeightForWidth())
        self.spinBox_y.setSizePolicy(sizePolicy)
        self.spinBox_y.setFont(font)
        self.spinBox_y.setAlignment(Qt.AlignCenter)
        self.spinBox_y.setMinimum(1)
        self.spinBox_y.setMaximum(99)
        self.spinBox_y.setValue(5)
        self.spinBox_y.setDisplayIntegerBase(10)

        self.gridLayout.addWidget(self.spinBox_y, 2, 2, 1, 1)

        self.labelPosY = QLabel(self.centralwidget)
        self.labelPosY.setObjectName(u"labelPosY")
        sizePolicy.setHeightForWidth(self.labelPosY.sizePolicy().hasHeightForWidth())
        self.labelPosY.setSizePolicy(sizePolicy)
        self.labelPosY.setFont(font2)
        self.labelPosY.setAlignment(Qt.AlignLeading|Qt.AlignLeft|Qt.AlignVCenter)

        self.gridLayout.addWidget(self.labelPosY, 6, 1, 1, 1)

        self.label_2 = QLabel(self.centralwidget)
        self.label_2.setObjectName(u"label_2")
        self.label_2.setFont(font)

        self.gridLayout.addWidget(self.label_2, 0, 1, 1, 1)

        MainWindow.setCentralWidget(self.centralwidget)
        self.menubar = QMenuBar(MainWindow)
        self.menubar.setObjectName(u"menubar")
        self.menubar.setGeometry(QRect(0, 0, 875, 24))
        MainWindow.setMenuBar(self.menubar)
        self.statusbar = QStatusBar(MainWindow)
        self.statusbar.setObjectName(u"statusbar")
        MainWindow.setStatusBar(self.statusbar)

        self.retranslateUi(MainWindow)
        self.pushButton.clicked.connect(MainWindow.close)

        QMetaObject.connectSlotsByName(MainWindow)
    # setupUi

    def retranslateUi(self, MainWindow):
        MainWindow.setWindowTitle(QCoreApplication.translate("MainWindow", u"MainWindow", None))
        self.label.setText(QCoreApplication.translate("MainWindow", u"n\u306e\u5024", None))
        self.label_3.setText(QCoreApplication.translate("MainWindow", u"m\u306e\u5024", None))
        self.pushButton.setText(QCoreApplication.translate("MainWindow", u"Quit", None))
        self.labelPosX.setText("")
        self.labelPosY.setText("")
        self.label_2.setText(QCoreApplication.translate("MainWindow", u"Parameter", None))
    # retranslateUi
```
:::
