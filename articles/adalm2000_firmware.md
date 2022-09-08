---
title: "ADALM2000のアップデートに失敗して起動しなくなった際の対処"
emoji: "🔨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ADALM2000","RaspberryPi4","dfu-util"]
published: true
---

# これは何？

この記事は、Analog DevicesのADALM2000のファームウェアアップデートに失敗して起動できなくなった際の対処法の一例をまとめたものです。

 [ココ](https://ez.analog.com/adieducation/university-program/f/q-a/120005/adalm-2000-when-i-update-the-firmware-i-turn-off-the-power-during-led-blinking-by-mistaken-after-that-no-working-how-to-be-alive-it-again)の書き込みを参考にし、DFUモードでファームウェアを書き込んだら無事に復旧しました。

## 経緯

眠気と戦いながら、とあるデータ収集の実験をしていたときに、気分転換にファームウェアのアップデートでもしようかなと思ったことがきっかけでした。

ドキュメントをみながら作業をしていて、ドライブのアンマウントをしたあと、USBケーブルを抜いてもう一回つなぐとアップデートが始まるのねー、と軽い気持ちでADALM2000のUSBケーブルを引っこ抜きました。んで、USBケーブルをつないだところうんともすんともいわなくなりまして、1万ウン千円のデバイスが、LEDが点灯しているだけのただの箱状態に。。。
あわててドキュメントを読むと、


![](/images/2022-09-09-06-46-34.png)
と
![](/images/2022-09-09-06-46-45.png)


との記述を発見！（っていうほど小さい字で書いてあったわけではなく、しっかりと書いてありましたよ）

結果的に、やってはいけないと書いてある「パイロットランプが点滅中にUSBケーブルを引っこ抜く」ことをやってしまい、ADALM2000が起動しなくなってしまいました。

[ドキュメント](https://wiki.analog.com/university/tools/pluto/users/firmware)によると、ファームウェアの破損によりDFUモード(the DONE LED is OFF, while LED1 is constantly ON)に入っているみたい。ということでDFUモードでファームウェアの書き込みを行います。

まあ、こんなアホなことをする方はほとんどいないと思いますが、自分用の備忘録に記録をしておきます。


## 環境

- ADALM2000
- Raspberry Pi 4 4GB
- ADALM2000の使用環境は、macOS Catalina

## 手順の概略

1. ラズパイ4にADALM2000をUSB接続
2. dfu-utilをインストール
3. 最新のファームウェアのアーカイブをダウンロード
4. ファ－ムウェアを書き込み
5. ADALM2000を再起動

# 実際の作業


## ラズパイにADALM2000を接続

おもむろにADALM2000を接続してみます。
しばらく間をあけてdmesgを実行し、該当するデバイスが認識されていることを確認

```sh
$ dmesg
（略）
[  509.309874] usb 1-1.4: new high-speed USB device number 8 using xhci_hcd
[  509.410428] usb 1-1.4: New USB device found, idVendor=0456, idProduct=b675, bcdDevice= 2.21
[  509.410437] usb 1-1.4: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[  509.410442] usb 1-1.4: Product: USB download gadget
[  509.410447] usb 1-1.4: Manufacturer: Analog Devices Inc.
```

次に、lsusbでUSBデバイスのリストを確認。Device 008に認識されています。

```sh
$ lsusb
Bus 002 Device 002: ID 8564:1000 Transcend Information, Inc. JetFlash
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 008: ID 0456:b675 Analog Devices, Inc.
Bus 001 Device 003: ID 17ef:6047 Lenovo
Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

## dfu-utilのインストール

汎用のソフトウェアでパッケージ化されていますので、aptからインストールできます。

```sh
$ sudo apt install dfu-util
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています
状態情報を読み取っています... 完了
以下のパッケージが新たにインストールされます:
  dfu-util
アップグレード: 0 個、新規インストール: 1 個、削除: 0 個、保留: 38 個。
33.1 kB のアーカイブを取得する必要があります。
この操作後に追加で 120 kB のディスク容量が消費されます。
取得:1 http://deb.debian.org/debian buster/main arm64 dfu-util arm64 0.9-1 [33.1 kB]
33.1 kB を 0秒 で取得しました (91.9 kB/s)
以前に未選択のパッケージ dfu-util を選択しています。
(データベースを読み込んでいます ... 現在 95656 個のファイルとディレクトリがインストールされています。)
.../dfu-util_0.9-1_arm64.deb を展開する準備をしています ...
dfu-util (0.9-1) を展開しています...
dfu-util (0.9-1) を設定しています ...
man-db (2.8.5-2) のトリガを処理しています ...
```

まずは、DFUデバイスのリストを表示

```sh
$ sudo dfu-util --list
dfu-util 0.9

Copyright 2005-2009 Weston Schmidt, Harald Welte and OpenMoko Inc.
Copyright 2010-2016 Tormod Volden and Stefan Schmidt
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Found DFU: [0456:b675] ver=0221, devnum=8, cfg=1, intf=0, path="1-1.4", alt=4, name="spare.dfu", serial="UNKNOWN"
Found DFU: [0456:b675] ver=0221, devnum=8, cfg=1, intf=0, path="1-1.4", alt=3, name="uboot-env.dfu", serial="UNKNOWN"
Found DFU: [0456:b675] ver=0221, devnum=8, cfg=1, intf=0, path="1-1.4", alt=2, name="uboot-extra-env.dfu", serial="UNKNOWN"
Found DFU: [0456:b675] ver=0221, devnum=8, cfg=1, intf=0, path="1-1.4", alt=1, name="firmware.dfu", serial="UNKNOWN"
Found DFU: [0456:b675] ver=0221, devnum=8, cfg=1, intf=0, path="1-1.4", alt=0, name="boot.dfu", serial="UNKNOWN"
```
ちゃんと認識されています。

## ファームウェアの最新のアーカイブをダウンロード

最新のアーカイブは、[こちら](https://github.com/analogdevicesinc/m2k-fw/releases/tag/v0.26)から入手することができます。

アーカイブには以下のファイルが含まれています。


```sh
$ ls -l
合計 40284
-rw-rw-r-- 1 pi pi   469500  9月 21 17:40 boot.dfu
-rw-rw-r-- 1 pi pi   601613  9月 21 17:40 boot.frm
-rw-rw-r-- 1 pi pi 10068479  9月 21 17:40 m2k.dfu
-rw-rw-r-- 1 pi pi 10068496  9月 21 17:40 m2k.frm
-rw-r--r-- 1 pi pi   917520  9月 21 17:40 mtd2.dfu
-rw-rw---- 1 pi pi   131088  9月 21 17:40 uboot-env.dfu
```

書き込みが必要となるのは、dfuの拡張子のファイルだけです。


## ファームウェアの書き込み

問題なく認識されているので、まずはfirmwareの書き込みから


```sh
$ sudo dfu-util -a firmware.dfu -D ./m2k.dfu
dfu-util 0.9

Copyright 2005-2009 Weston Schmidt, Harald Welte and OpenMoko Inc.
Copyright 2010-2016 Tormod Volden and Stefan Schmidt
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Match vendor ID from file: 0456
Match product ID from file: b675
Opening DFU capable USB device...
ID 0456:b675
Run-time device DFU version 0110
Claiming USB DFU Interface...
Setting Alternate Setting #1 ...
Determining device status: state = dfuIDLE, status = 0
dfuIDLE, continuing
DFU mode device DFU version 0110
Device returned transfer size 4096
Copying data from PC to DFU device
Download	[=========================] 100%     10068463 bytes
Download done.
state(7) = dfuMANIFEST, status(0) = No error condition is present
state(2) = dfuIDLE, status(0) = No error condition is present
Done!
```
つづけて

```sh
$ sudo dfu-util -a boot.dfu -D ./boot.dfu
$ sudo dfu-util -a uboot-env.dfu -D ./uboot-env.dfu
$ sudo dfu-util -a uboot-extra-env.dfu -U ./uboot-extra-env.dfu
```

を実行します。

## 動作確認

問題なく書き込めたら、USBケーブルを抜いて、改めてMacに接続します。Scopyを起動しADALM2000が認識されていれば復旧完了です。

![](/images/2022-09-09-06-47-13.png)

# 教訓

眠いときの気分転換（現実逃避）にクリティカルな作業（ファームウェアのバージョンアップなど）を行うのはやめましょう。

