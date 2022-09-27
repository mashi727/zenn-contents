---
title: "MacでGo"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Go","Golang","Mac"]
published: true
---



# これは何？

MacにおけるGo言語のインストールの記録です。


# Goのインストール

brewのインストール[こちら](https://zenn.dev/mashi_z/articles/monterey_homebrew)

```sh
% brew install go
```
でインストールは完了です。

```sh
% go version
go version go1.19.1 darwin/amd64
```



# godocのインストール

godocは、Goのドキュメントサーバーです。
godocは、以下のようにインストールします。


```sh
% go install golang.org/x/tools/cmd/godoc@latest
```
バージョンの指定は必須です。

わたしの環境の場合、godocのパスをきる必要がありました。

```sh
export PATH="$HOME/go/bin:$PATH"
```
を、`~/.zshrc`に追加します。

godocを起動すると

```sh
% godoc
using module mode; GOMOD=/dev/null
```

となれば問題ありません。
ドキュメント・サーバーへのリンクは、[こちら](http://localhost:6060/)になります。
アドレスは、`http://localhost:6060/`です。

このような画面が立ち上がります。

![](/images/2022-09-27-20-36-38.png)
*godocサーバーのホームページ*


# Hello World的な


```sh
% mkdir -p ~/go/src/hello
% cd ~/go/src/hello
% vi hello.go
```
という感じで、hello.goを作成して、以下のコードを入力します。

```go
package main

import "fmt"

func main() {
    fmt.Printf("hello, world!\n")
}
```

実行します。

```sh
% go run hello.go
hello, world!
```

ビルドは、

```sh

% go build hello.go
% ls
hello.go  hello*
% ./hello
hello, world!
```

てな具合にできます。

