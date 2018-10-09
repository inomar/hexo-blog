---
title: Rails開発環境構築 Ruby編
date: 2018-05-07 17:46:33
tags: ['tech-log','Ruby']
---

転職してからRailsをメインで使うことになったのでまずは開発環境構築のログを残しておく


## 事前準備

- CentOS7
- Ruby 2.3.1
- Rails 4.2

~~~
# yum update
~~~

~~~
# yum install -y git openssl-devel readline-devel zlib-devel gcc libxml2-devel libxslt-devel mysql-devel libffi-devel.x86_64
~~~

---
## Ruby(rbenv)

必要なものは以下の2つ
- rbenv 
- ruby-build


### rbenvとは？
Rubyのバージョンをプロジェクトごとに切り替えたりと複数のバージョンを管理できるパッケージツール

### ruby-buildとは？
rbenvのプラグインで`rbenv install`のコマンド使えるようになる


### 使い方
今回はgithubのリポジトリからクローンして、それぞれディレクトリをしていしてやります。

~~~
$ git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
$ git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
~~~

 パスの設定(.bash_profile) 

~~~
$ export PATH="$HOME/.rbenv/bin:$PATH" >> ~/.bash_profile
$ eval "$(rbenv init -)" >> ~/.bash_profile ※1
~~~

~~~
$ source ~/.bash_profile
~~~

あとは使いたいRubyのバージョンをしてしてやるだけ
今回は2.3.1を使用したいので以下のコマンドを実行

~~~
$ rbenv install バージョン[2.3.1]
$ rbenv global バージョン[2.3.1]
~~~

ちなみに `local`とするとそのディレクトリに対するRubyのバージョンが指定されるとっても便利

~~~
$ rbenv local 2.3.1
~~~

~~~
$ ruby -v

~~~

これでRubyの準備ができました。


