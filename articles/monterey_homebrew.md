---
title: "macOS MontereyでHomebrewの再インストール"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Git","homebrew","macOS","Monterey"]
published: true
---

# これは何？

HomebrewがMontereyにも対応したようなので、古い環境を削除して新しいHomebrewをインストールするまでの記録です。

## きっかけ

gnuradioをHomebrewでインストールしようとすると、以下のメッセージが。

```sh
$ brew install gnuradio
Updating Homebrew...
Warning: You are using macOS 12.
We do not provide support for this pre-release version.
You will encounter build failures with some formulae.
Please create pull requests instead of asking for help on Homebrew's GitHub,
Twitter or any other official channels. You are responsible for resolving
any issues you experience while you are running this
pre-release version.
```

Homebrewのサイトを確認すると、

Today I’d like to announce Homebrew 3.3.0.
The most significant changes since 3.2.0 are the official support of macOS Monterey.

となっており対応している模様。

gnuradioは、macports推奨でして、GUIまでの環境構築は苦労しそうですが、macportsは依存関係のファイルも大量にインストールされるので、可能な限りOSのライブラリなどを使用するHomebrewが良いのでHomebrewを更新します。


## 手順

### 古いHomebrewの削除

おもむろに、以下のコマンドを実行します。

```sh
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

```sh
==> Homebrew uninstalled!
The following possible Homebrew files were not deleted:
/usr/local/Frameworks/
/usr/local/Homebrew/
/usr/local/bin/
/usr/local/etc/
/usr/local/include/
/usr/local/lib/
/usr/local/libexec/
/usr/local/man/
/usr/local/opt/
/usr/local/sbin/
/usr/local/share/
/usr/local/var/
You may wish to remove them yourself.
```

となりました。/usr/localへ自身で作成したファイルを保存していなければ問題ないので、削除します。
これで、アンインストールは終了です。


### Homebrewの新規インストール



https://brew.sh/index_ja のとおりにインストールすれば、問題ありません。
まずは、以下のコマンドを、

```sh
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

問題なくインストールされているか確認します。

```sh
$ brew doctor
Your system is ready to brew.
$ brew update
Already up-to-date.
$ brew upgrade
$ 
```
特にメッセージも出なければ、問題ありません。
必要なツールのインストールを行います。


### macOSにてbrew updateするとPermission deniedが表示される

再インストール後にbrew updateすると、以下のメッセージがでて困ったことがあります。

```sh
$ brew update
git@ssh.github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
Error: Fetching /usr/local/Homebrew failed!
Fetching /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask failed!
Fetching /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core failed!
```

まずは、sshの設定に問題がないことを確認しましょう。

```sh
$ ssh -vT ssh.github.com
OpenSSH_8.6p1, LibreSSL 2.8.3
（略）
Hi YOURID! You've successfully authenticated, but GitHub does not provide shell access.
debug1: channel 0: free: client-session, nchannels 1
Transferred: sent 3684, received 2968 bytes, in 0.3 seconds
Bytes per second: sent 12200.9, received 9829.6
debug1: Exit status 1
```


問題なければ、以下の記事を参考にして

https://github.com/Homebrew/brew/issues/52


![](/images/2022-09-09-06-06-01.png)


githubのキーを

```sh
$ ssh-add ~/.ssh/id_rsa_git
```
とすれば、解決します。

ssh-addのmanを貼っておきます。

> ssh-add は、認証エージェントである ssh-agent(1) に秘密鍵の ID を追加します。
引数を指定すると、 ~/.ssh/id_rsa, ~/.ssh/id_dsa, ~/.ssh/id_ecdsa, ~/.ssh/id_ecdsa_sk というファイルが追加されます。
~/.ssh/id_ed25519 および ~/.ssh/id_ed25519_sk です。 秘密鍵を読み込んだ後、ssh-add は以下を読み込もうとします。
証明書名に-cert.pubを付加したファイル名から、対応する証明書情報を取得します。秘密鍵ファイル  コマンドラインから別のファイル名を指定することもできます。パスフレーズを必要とするファイルがある場合、ssh-add はユーザからパスフレーズを要求します。 パスフレーズはssh-add は、複数の ID ファイルが指定された場合、最後のパスフレーズを再試行します。認証エージェントが実行されており、SSH_AUTH_SOCK 環境変数にそのパスフレーズが含まれている必要があります。

# その後

## zsh compinit: insecure directoriesへの対処

`.zshrc` のリロードを行うと、`zsh compinit: insecure directories`が表示されるようになりました。

```sh
$ compaudit
There are insecure directories:
/usr/local/share
```

とのことでしたので、

```sh
$ sudo chmod 755 /usr/local/share
```

としました。

## Homebrewがpyenvのconfigファイルを気にする件

```sh
$ brew doctor
Please note that these warnings are just used to help the Homebrew maintainers
with debugging if you file an issue. If everything you use Homebrew for is
working fine: please don't worry or file an issue; just ignore this. Thanks!

Warning: "config" scripts exist outside your system or Homebrew directories.
`./configure` scripts often look for *-config scripts to determine if
software packages are installed, and which additional flags to use when
compiling and linking.

Having additional scripts in your path can confuse software installed via
Homebrew if the config script overrides a system or Homebrew-provided
script of the same name. We found the following "config" scripts:
  /Users/mashi/.pyenv/shims/python3.9-config
  /Users/mashi/.pyenv/shims/python-config
  /Users/mashi/.pyenv/shims/python3-config
  /Users/mashi/.pyenv/shims/pybind11-config
  /Users/mashi/.pyenv/shims/python3.8-config
```

pyenvのconfigファイルを見つけて 「config" スクリプトはあなたのシステムや Homebrew ディレクトリの外にも存在します」とお知らせしてくれてるのですが、Homebrew君だけがconfigを使うわけではないので気にしなくて良いのですがね。

ということなのですが気持ち悪いのでbrewから見えなくして差し上げるために、コマンドのaliasを設定します。

```.zshrc
alias brew='PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/usr/local/sbin:/sbin brew'
```
上の一文を`.zshrc`などの適当な位置に書き込みます。

```sh
$ source ~/.zshrc
$ brew doctor
Your system is ready to brew.
```

これで大丈夫かと。