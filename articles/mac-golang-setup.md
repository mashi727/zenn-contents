---
title: "Macã§Go"
emoji: "ð»"
type: "tech" # tech: æè¡è¨äº / idea: ã¢ã¤ãã¢
topics: ["Go","Golang","Mac"]
published: true
---



# ããã¯ä½ï¼

Macã«ãããGoè¨èªã®ã¤ã³ã¹ãã¼ã«ã®è¨é²ã§ãã


# Goã®ã¤ã³ã¹ãã¼ã«

brewã®ã¤ã³ã¹ãã¼ã«[ãã¡ã](https://zenn.dev/mashi_z/articles/monterey_homebrew)

```sh
% brew install go
```
ã§ã¤ã³ã¹ãã¼ã«ã¯å®äºã§ãã

```sh
% go version
go version go1.19.1 darwin/amd64
```



# godocã®ã¤ã³ã¹ãã¼ã«

godocã¯ãGoã®ãã­ã¥ã¡ã³ããµã¼ãã¼ã§ãã
godocã¯ãä»¥ä¸ã®ããã«ã¤ã³ã¹ãã¼ã«ãã¾ãã


```sh
% go install golang.org/x/tools/cmd/godoc@latest
```
ãã¼ã¸ã§ã³ã®æå®ã¯å¿é ã§ãã

ãããã®ç°å¢ã®å ´åãgodocã®ãã¹ãããå¿è¦ãããã¾ããã

```sh
export PATH="$HOME/go/bin:$PATH"
```
ãã`~/.zshrc`ã«è¿½å ãã¾ãã

godocãèµ·åããã¨

```sh
% godoc
using module mode; GOMOD=/dev/null
```

ã¨ãªãã°åé¡ããã¾ããã
ãã­ã¥ã¡ã³ãã»ãµã¼ãã¼ã¸ã®ãªã³ã¯ã¯ã[ãã¡ã](http://localhost:6060/)ã«ãªãã¾ãã
ã¢ãã¬ã¹ã¯ã`http://localhost:6060/`ã§ãã

ãã®ãããªç»é¢ãç«ã¡ä¸ããã¾ãã

![](/images/2022-09-27-20-36-38.png)
*godocãµã¼ãã¼ã®ãã¼ã ãã¼ã¸*


# Hello Worldçãª


```sh
% mkdir -p ~/go/src/hello
% cd ~/go/src/hello
% vi hello.go
```
ã¨ããæãã§ãhello.goãä½æãã¦ãä»¥ä¸ã®ã³ã¼ããå¥åãã¾ãã

```go
package main

import "fmt"

func main() {
    fmt.Printf("hello, world!\n")
}
```

å®è¡ãã¾ãã

```sh
% go run hello.go
hello, world!
```

ãã«ãã¯ã

```sh

% go build hello.go
% ls
hello.go  hello*
% ./hello
hello, world!
```

ã¦ãªå·åã«ã§ãã¾ãã

