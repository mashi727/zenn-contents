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

# 作成の背景


休日に時間があると自転車に乗っています。遠出をすることも多いのでルートを検索して行程を決めるのですが、その際に検索したルートの標高や斜度を調べる良いツールをなかなか見つけられずにおります。

Pythonで地図を作成したことがなかったためこれを機につくってみようと思いたったのがきっかけです。アプリは、以下のような感じで作ろうと考えておりまして

1. PySide6のウィジェットに地図を埋め込み
1. ルート検索は、OpenrouteserviceもしくはGoogle My Mapsで検索をしてkmlエクスポートから読み込み
1. 斜度などを計算してPyQtGraphにて可視化

この記事では、その中のPySide6に地図を埋め込むところまでになります。


# 実際に制作

少し丁寧に順をおって実装していきましょう。


# 遭遇した事象


## designerから格上げできない

foliumをPySide6のウィジェットに表示は、以下のようにマップオブジェクト（ここでは、m）を作成してQWebEngineViewを使用する必要があります。

```python
data = io.BytesIO()
m.save(data, close_file=False)
self.view.setHtml(data.getvalue().decode())
```

このような場合には、QWidgetをQWebEngineViewに格上げして表示領域を確保するのがQtの流儀との認識ですが、QWebEngineViewを一度格上げしたところ、macOSでは以降からこんなメッセージが出るようになり通常の手順で格上げできなくなりました。

![](/images/2022-09-10-21-47-59.png)



dockerに構築した環境でも発生しましたが、dockerのイメージを再構築して試したところ再現せず問題なく使用できています。
どこかに設定が保存されているのでしょうが、見つけきれず再現性もなさそうなので取り急ぎの対応で済ませることとしました。

（注）そもそも、dockerの環境ではOpenGLが落ちるので、Web関係はuiの編集しかできていません。。。


PySideはPyQtのようにuiファイルを直接読み込みできないので、解決方法としては以下の2点をとります。

- uiファイルもしくはuicにて生成されるpyファイルに格上げと同等の変更を行う
- uiを読み込む側のコードにて格上げに相当する処理を追加



おすすめはpyファイルの編集、もしくはuiを読み込む側で処理を行うことです。


## uiもしくはpyファイルの編集




### uiファイルの編集

格上げを行う前と後の差分は以下のとおりなので、差分をuiファイルに追加すればOKです。


```sh
worker@pyside6:~/py_works/route$ diff simpleWebUi_Docker1_nopromote.ui simpleWebUi_Docker1.ui
19c19
<      <widget class="QWidget" name="widget" native="true"/>
---
>      <widget class="QWebEngineView" name="widget" native="true"/>
34a35,42
>  <customwidgets>
>   <customwidget>
>    <class>QWebEngineView</class>
>    <extends>QWidget</extends>
>    <header location="global">qwebengineview.h</header>
>    <container>1</container>
>   </customwidget>
>  </customwidgets>
```


参考まで、それぞれのuiファイルをあげておきます。


<details><summary>格上げ前</summary><div>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ui version="4.0">
 <class>MainWindow</class>
 <widget class="QMainWindow" name="MainWindow">
  <property name="geometry">
   <rect>
    <x>0</x>
    <y>0</y>
    <width>800</width>
    <height>600</height>
   </rect>
  </property>
  <property name="windowTitle">
   <string>MainWindow</string>
  </property>
  <widget class="QWidget" name="centralwidget">
   <layout class="QGridLayout" name="gridLayout">
    <item row="0" column="0">
     <widget class="QWidget" name="widget" native="true"/>
    </item>
   </layout>
  </widget>
  <widget class="QMenuBar" name="menubar">
   <property name="geometry">
    <rect>
     <x>0</x>
     <y>0</y>
     <width>800</width>
     <height>18</height>
    </rect>
   </property>
  </widget>
  <widget class="QStatusBar" name="statusbar"/>
 </widget>
 <resources/>
 <connections/>
</ui>
```

</div></details>



<details><summary>格上げ後</summary><div>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ui version="4.0">
 <class>MainWindow</class>
 <widget class="QMainWindow" name="MainWindow">
  <property name="geometry">
   <rect>
    <x>0</x>
    <y>0</y>
    <width>800</width>
    <height>600</height>
   </rect>
  </property>
  <property name="windowTitle">
   <string>MainWindow</string>
  </property>
  <widget class="QWidget" name="centralwidget">
   <layout class="QGridLayout" name="gridLayout">
    <item row="0" column="0">
     <widget class="QWebEngineView" name="widget" native="true"/>
    </item>
   </layout>
  </widget>
  <widget class="QMenuBar" name="menubar">
   <property name="geometry">
    <rect>
     <x>0</x>
     <y>0</y>
     <width>800</width>
     <height>18</height>
    </rect>
   </property>
  </widget>
  <widget class="QStatusBar" name="statusbar"/>
 </widget>
 <customwidgets>
  <customwidget>
   <class>QWebEngineView</class>
   <extends>QWidget</extends>
   <header location="global">qwebengineview.h</header>
   <container>1</container>
  </customwidget>
 </customwidgets>
 <resources/>
 <connections/>
</ui>

```

</div></details>

