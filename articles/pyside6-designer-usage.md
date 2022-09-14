---
title: "PySide6 Designerの使い方（1）"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["PySide6","Designer","Python"]
published: false
---

# これは何？

PySide6でGUIを作成する際に使用しているDesignerは、使うたびに「ん？どうするんだっけ」となっておりますので、使う際に考えたことをメモ的に記録します。

# Disignerの使い方



## pyside6-desinerによる画面の設定

```python
% pyside6-designer
```
と入力してdesignerを起動して、Main Windowを選択します。

![](/images/2022-09-12-07-18-39.png)

デフォルトですと、画面が狭く作業しづらいので画面を大きくします。
画面の大きさは起動時に設定することも可能ですが、ここで設定してしまいましょう。

![](/images/2022-09-12-07-20-58.png)


画面の大きさは画面をクリックしたのち、右側にあるプロパティエディタのgeometryにて設定します。

![](/images/2022-09-12-07-26-50.png)

ここでは1200x960にします。

### ウィジェットの配置

いよいよ、ウィジェットを配置します。完成イメージは、以下の通りです。

![](/images/2022-09-12-07-30-56.png)


#### QWidgetを配置して格上げ

![](/images/2022-09-12-07-33-13.png)

以下のメッセージが出た場合は、

![](/images/2022-09-12-07-33-51.png)



# 遭遇した事象


## designerから格上げできない

foliumをPySide6のウィジェットに表示は、以下のようにマップオブジェクト（ここでは、m）を作成してQWebEngineViewを使用する必要があります。

```python
data = io.BytesIO()
m.save(data, close_file=False)
self.view.setHtml(data.getvalue().decode())
```

このような場合には、QWidgetをQWebEngineViewに格上げしてウィジェットを配置するのですが、別のアプリケーションを作成した際にQWebEngineViewを一度格上げしたところ、macOSでは以降から以下のメッセージが出るようになり通常の手順で格上げできなくなりました。

![](/images/2022-09-10-21-47-59.png)


dockerに構築したPySide6の環境でも発生しましたが、dockerのイメージを再構築して試したところ再現せず問題なく使用できています。とすると、どこかに設定が保存されていると推測されるのですが、見つけきれず再現性もなさそうなので取り急ぎの対応で済ませることとしました。

（注）そもそも、dockerの環境でPySideのアプリケーションを起動すると、OpenGLが落ちるのでWeb関係はdesignernによるuiの編集しか行うことができません。

PySideはPyQtのようにuiファイルを直接読み込みできないので、解決方法としては以下の2点をとります。

- pyファイル（uicにて生成される）に格上げと同等の変更を行う
- uiファイルに格上げに相当する処理を追加

ここでは、uiに修正を行います。


## uiファイルの編集

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


:::details 格上げ前

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

:::


:::details 格上げ後

QWidgetが、QWebEngineViewに
```<customwidgets>```のタグが追加されています。



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

:::

