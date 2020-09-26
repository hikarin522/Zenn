---
title: "GitのSJIS対応メモ"
emoji: "♻️"
type: "tech"
topics: ["Git", "VSCode"]
published: true
---

# 環境
OS: Windows7
Git: 2.15.0.windows.1
VS Code: 1.18.1
PSVersion: 5.1.14409.1012

# git config
```
[core]
# 日本語ファイル名対応
    quotepath=false

# ページャの文字化け対応
    pager=LC_ALL=ja_JP.UTF-8 less -Sx4

# gitk, git-guiの設定
[gui]
    encoding=utf-8

# git diff, git show 時に文字コード変換
[diff "cp932"]
    textconv=iconv -f cp932 -t utf-8
[diff "sjis"]
    textconv=iconv -f sjis -t utf-8
```

# VS Code
`git show` 時に `--textconv` のオプションが付いてない問題の対応
`C:\Program Files\Microsoft VS Code\resources\app\extensions\git\out\git.js`
`Repository::buffer(object, encoding = 'utf8')`
L413: `const child = this.stream(['show', object]);` を
`const child = this.stream(['show', object, '--textconv']);` に変更
[https://github.com/Microsoft/vscode/blob/1.18.1/extensions/git/src/git.ts#L543](https://github.com/Microsoft/vscode/blob/1.18.1/extensions/git/src/git.ts#L543)

# .gitattributes

```
# リポジトリ内のsjisファイルを指定
*.txt diff=sjis
*.c diff=cp932
*.h diff=cp932
```

# 未解決の問題
- VS Codeのdiff  
VS Codeでのdiff表示時に全角チルダ`～`の変換がうまくいってない。  
iconvをnkfにしてもダメだった。  
> とりあえず git config を
> `textconv = "iconv_cp932() { iconv -c -f cp932 -t utf-8 $1 | sed 's/〜/～'/g; }; iconv_cp932"`
> にすると直るけどなんかあれ :innocent:  
> `－`も追加
> `	textconv = "iconv_cp932() { iconv -c -f cp932 -t utf-8 $1 | sed -e 's/〜/～/g' -e 's/−/－/g'; }; iconv_cp932"`

- git add -p  
git add -p 内部でのgit diffでiconvが呼ばれていない。  
`$ git config --global interactive.diffFilter "iconv -f cp932 -t utf8"`  
とすると直るがこれだと全てcp932になってしまう。  
> iconvじゃなくてnkfを使うと入力側文字コードを自動判定してくれるので  
> `$ git config --global interactive.diffFilter "nkf -w"`
> とするととりあえずはなんとかなる。  
> ※ Git: 2.15.0.windows.1にnkfは付いてないので別で入れて下さい。

# 追記

最近sjisのファイル触らんくなったからあんま分からんけど `.gitattributes` で `working-tree-encoding` の設定するのがよさそう。
https://git-scm.com/docs/gitattributes#_code_working_tree_encoding_code
