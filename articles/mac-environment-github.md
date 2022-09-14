---
title: "macOS MontereyにてGithubの設定"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Monterey","github"]
published: true
---


# これは何？

macOS Montereyにgithubの設定を行ったさいの私的メモです。いつも忘れるのでメモしました。
最近のgithubは、sshで認証を行う必要がありますので、sshに関する設定を行います。

gitのインストールは、済んでいるものとします。
私はOSとともにインストールされるものとは別のgitを使用しています。



# github向けの初期設定

```sh
$ git config --global user.name "YOUR_NAME"
$ git config --global user.email "YOUR_EMAIL"
$ git config --global core.quotepath false
$ git config --global url."git@github.com:".insteadOf "https://github.com/"
```

# 公開鍵の作成

以下のコマンドを実行すると、のファイル名を聞かれるので`id_rsa_git`としています。
その後、パスフレーズが２度聞かれるので適切に設定します。
（忘れないようにしてください。）

メールアドレスは、ご自身のものをご使用ください。

また、以下の作業を行うディレクトリは`~/.ssh`の中が良いでしょう。


```sh
$ ssh-keygen -t rsa -C "YOUR_EMAIL"
```

成功すると、公開鍵が作成されます。

```sh
$ ssh-keygen -t rsa -C "YOUR_EMAIL"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/xxxx/.ssh/id_rsa): id_rsa_git
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa_git
Your public key has been saved in id_rsa_git.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx YOUR_EMAIL
The key's randomart image is:
+---[RSA 3072]----+
|XXX              |
|XXX              |
|XXX              |
|XXX              |
|XXXXXXXXX        |
|XXXXXXXX         |
|XXXXXXXXX        |
|XXXXXXXX         |
|XXXXXXXX         |
+----[SHA256]-----+
```

出来上がるファイルは、以下のとおりです。

```sh
$ ls
id_rsa_git id_rsa_git.pub
```

両ファイルとも600でないと、認証の際に`Load key "/Users/ユーザー名/.ssh/id_rsa_git": bad permissions`と叱られます。


# GitHubへ公開鍵を登録


いよいよ先ほど生成された公開鍵をクリップボードにコピーします。

```sh
$ pbcopy < ~/.ssh/id_rsa_git.pub
```

GitHubへログインし、Settingを開きます。

![](/images/2022-09-14-20-32-20.png)

以下の画面のSSH and GPG keysを選択し、


![](/images/2022-09-14-20-32-34.png)

New SSH keyをクリックします。

![](/images/2022-09-14-20-32-47.png)

タイトルとKeyを設定します。

タイトルはPCの識別名を、Keyは先ほどクリップボードにコピーした`id_rsa_git.pub`の内容を貼り付けます。
Add SSH keyをクリックすると以下の画面となり、先ほどのSSH key登録のが完了します。


![](/images/2022-09-14-20-33-02.png)

# 接続の確認

ここで、シェルから接続の確認を行います。

```sh
$ ssh -T ssh.github.com
Hi YourNAME! You've successfully authenticated, but GitHub does not provide shell access.
```

となれば、完了です。


#  OS再インストール時の再設定

OSをクリーンインストールなどした場合は、これらのファイルと`~/.ssh/config`を`~/.ssh`以下にコピーすれば設定終了です。
