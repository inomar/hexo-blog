---
title: hugoを使ってGitHub Pagesページを書き換えてみた
date: 2017-08-25 17:22:28
tags: ['tech-log', 'Hugo']
---
Railsの勉強も兼ねてblogを作ったものの、バグを修正したり、レイアウト変えてみたりと  
少し修正を加えるだけでも運用はめんどくさい  

ということで気軽にwebコンテンツが作成できる`hugo`を試してみた。


## hugoとは？

> HUGOは、静的なhtmlを生成する事ができる静的ページジェネレータです。HUGOの場合、ローカル環境ではHUGOの動的機能を使って動的にサイトを開発し、成果物の出力時に動的要素を全て静的要素（html,css,js,img等）として出力します。 生成された成果物にはサーバサイドの処理を含まないため、サイトの表示速度は高速になり、サーバサイドの処理が無い分セキュリティリスクが減ります。
[Let's study HUGO!!](http://www.study-hugo.com/basic/whats-hugo/)

hugoを使えば簡単にwebコンテンツが作れて幸せになれるんだね。。。

ということで今回はブログの代わりになるサイト?を作ってみようと思います！

## ゴール
1. hugoを使ってblogを作成
2. github pages(inomar.github.io)へホスティング
3. この記事を投稿してみる

## hugoをインストール
環境がmacなので導入にはhomebrewを使います。

```
$ brew insatll hugo
```

### hugoコマンド
この4つさえ覚えればサイトの作成と確認ができます

```bash
$ hugo new site site_name #適当なサイト名をつけます

$ hugo new post/_index.md # コンテンツを作成します

$ hugo # buildしてconfig.tomlでpublishDirに設定した公開用のディレクトリが生成されます。

$ hugo server # ブラウザでhttp://localhost:1313でbuild内容を確認できます
```


## サイトを作成

```bash
$ hugo new site site_name #適当なサイト名をつけます

$ cd site_name

# ディレクトリ構造
$ tree
.
├── archetypes  # メタデータを設定できる
│   └── default.md
├── config.toml # 設定ファイル
├── content     # ブログやらのコンテンツはこのディレクトリに入る
├── data　　　　  #
├── layouts     # headerやfooterなどサイトのレイアウト
├── static　　　 # cssや画像など
└── themes      # hugoで用意されたテーマ https://themes.gohugo.io/

```

今回はすでに用意してあるこちらのテーマを使って作っていきます。
[cocoa-hugo-theme - github](https://github.com/nishanths/cocoa-hugo-theme)

```bash
$ git clone https://github.com/nishanths/cocoa-hugo-theme.git themes/cocoa
```
これでthemesディレクトリに`cocoa`のテーマがcloneされるので
あとは`config.toml`を設定してやるだけです。
cocoaの設定は以下を参考にしてください。
(config.tomlの内容は丸々コピぺでもいいですが、faviconやavatarなどで画像のパスをしてしているので、そのままだとbuild時にエラーになってしまいます。パスはコメントアウトするか、`static`ディレクトリ内に`img`ディレクトリを作成し、好きな画像をいれてパスを設定しましょう。)
[cocoa-hugo-theme/exampleSite/config.toml](https://github.com/nishanths/cocoa-hugo-theme/blob/master/exampleSite/config.toml)

```bash
$ hugo # build

$ hugo server # localhost:1313を確認
```

この時点で`localhost:1313`を叩けば設定した内容が確認できるかと思います。


## Github pagesにホスティング
前提として
1. githubアカウントを持っていること
2. {user_name}.github.ioのリポジトリを作成していること

意外とここで苦しめられて納得のいく形にはなっていないのですが、書いていきます。

まずconfig.tomlに以下を設定する
```toml
publishDir              = "docs"
```
これはbuildした際に公開用のディレクトリの設定になります。(デフォルトだとpublicかな？)

```bash
# build
$ hugo

# 公開用のディレクトリにgit対象とし、masterへpushする
$ cd docs

$ git init

$ git remote add origin git@github.com:{user_name}/{user_name}.github.io.git

$ git add --all

$ git commit -m"initial commit"

$ git push origin master
```

これで`https://{user_name}.github.io`でローカルで確認した内容と同様のものが確認できるかと思います。
ただgitの管理対象が`docs`内のもののみになっていて、ソースそのものを管理する場合どうすればいいのかというところでかなり悩みました。
色々調べてみると別のブランチを切って管理しているみたいなので、その方法でやってみたいと思います。

### developブランチを切る
```bash
# サイトのrootディレクトリに戻ります
$ cd ..

# docsやthemesなどは管理対象からはずします。
$ vim .gitignore
# 以下を追加
themes
docs


$ git init

$ git remote add origin git@github.com:{user_name}/{user_name}.github.io.git

$ git checkout -b develop

$ git add --all

$ git commit -m"initial commit develop"

$ git push origin develop
```

これで冗長な気がしますが、`develop`でソース、`master`で公開用を管理することができました。

## ブログを投稿してみる
最後にブログを投稿してみようと思います。
```bash
# content/blogディレクトリ内に20170824.mdを作成する
$ hugo new blog/20170824.md
```

以下が実際に作成された内容です

```
---
title: "20170824"
date: 2017-08-24T17:21:51+09:00
draft: true
---
```
ここでは`20170824`というタイトルのコンテンツを作成しました。
`title`: ブログコンテンツのタイトル
`date` : 登校日
`draft`: 公開の有無(デフォルトはtrueで下書き状態なので、falseに変えてやることで公開が可能です。)

`draft`をfalseにし、コンテンツの内容を書き込んで、最後に`hugo`でbuildしてやれば
`localhost:1313/blog/20170824/`で確認できると思います。


これで目標は達成できたのですが、問題は管理方法ですよね。。。

## 所管
気軽に作成、投稿できてしまうのはかなりいいんじゃないかと思います。
特にサーバーサイドの知識を必要とされる訳でもないのでテーマを少しいじるだけでオリジナルのサイトが簡単に作れてしまうので、ワードプレスなんかよりは取り組みやすいかなと思います。