---
title: "githubでやらかしたcommitを削除する"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["github","rebase","macOS"]
published: true
---
# これは何？

`github`で、やらかしたcommitを削除したときのメモです。

今回は、APIキーをハードコーディングしたままでcommitしてしまいました。


# 行った手順

## git logで履歴の確認

```sh
%  git log --oneline
xxxxxxx (HEAD -> main) tmp
yyyyyyy (origin/main) Nasdaqシンボルの検索機能の実装まで（表示を整えるのは後ほど）
zzzzzzz (tag: create_ui) 1st commit for fin
```


戻したい識別番号にrebaseします。
エディタが開いたら、pickをdropに変更して実行します。

```sh
% git rebase -i zzzzzzz
Successfully rebased and updated refs/heads/main.
```

`git log`にて

```sh
% git log --oneline
zzzzzzz (HEAD -> main, tag: create_ui) 1st commit for fin
```

となり、もとに戻っていることが確認できます。
pushしましょう。

```sh
% git push origin main
To github.com:mashi727/finance_basic.git
 ! [rejected]        main -> main (non-fast-forward)
```

普通にpushすると、リジェクトされるので

```sh
% git push --force origin main
```

とします。


## 注意

rebaseしても、githubにはその記録がしっかりと残っています💦


```sh
% git reflog
zzzzzzz (HEAD -> main, tag: create_ui, origin/main) HEAD@{0}: rebase (finish): returning to refs/heads/main
zzzzzzz (HEAD -> main, tag: create_ui, origin/main) HEAD@{1}: rebase (start): checkout zzzzzzz
wwwwwww HEAD@{2}: rebase (finish): returning to refs/heads/main
wwwwwww HEAD@{3}: rebase (start): checkout zzzzzzz
wwwwwww HEAD@{4}: rebase (finish): returning to refs/heads/main
wwwwwww HEAD@{5}: rebase (start): checkout zzzzzzz
wwwwwww HEAD@{6}: commit: tmp
852e34e HEAD@{7}: commit: Nasdaqシンボルの検索機能の実装まで（表示を整えるのは後ほど）
zzzzzzz (HEAD -> main, tag: create_ui, origin/main) HEAD@{8}: commit (initial): 1st commit for fin
```

識別番号がわかるとそのコミットを見ることができますが、識別番号が漏れることはほぼないと思われますのでよしとします。

# 教訓

APIキーやアクセストークンをハードコーディングしないように。
別ファイルに記載するなどして、.gitignoreに追加しましょう。

pythonの場合、

```
apikey_path = './.alpha_vantage_apikey'
with open(apikey_path) as f:
    API_KEY = f.readlines()
print(type(API_KEY[0]),API_KEY[0])
```

こんな感じで良いでしょう。
`.alpha_vantage_apikey`の一行目に、APIキーを記載しています。

`.gitignore`ファイルに追加するのを忘れないようにします。
黒歴史が増えます。
