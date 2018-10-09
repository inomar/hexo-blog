---
title: rails5.1でwebpackが使用できるのでVuejsで遊んでみた
date: 2018-10-09 17:25:46
tags: ['tech-log', 'Rails', 'Vue']
---

現在取りかかっている個人開発に向けてRails + Vueという構成を考えているので、いろいろ調べていたら、rails5.1からwebpackが公式サポートされたということなので、今回は早速試してみた。  

## 目的
- webpackerの導入  
- 個人開発でRails + Vueを使いたい  

## 環境
- MacOS X    
- Ruby  2.4.0    
- Rails 5.1.2    
- node  7.5.0  
- npm   4.5.0  


## 導入

```
# とりあえずwebpackを導入したい場合
bundle exec rails new sample_app --webpack

# Vuejsを最初から設定したい場合
bundle exec rails new sample_app --webpack=vue
```


## webpacker:install:vue

```
bundle exec rails webpacker:install:vue
```

これを実行すると以下のようなファイルが生成される。  
`development.js`,`production.js`が`webpack.config.js`  

```
app/javascripts/pack
  ├── app.vue
  ├── application.js
  └── hello_vue.js

config/webpack
  ├── configuration.js
  ├── development.js
  ├── loaders
  │   ├── assets.js
  │   ├── babel.js
  │   ├── coffee.js
  │   ├── erb.js
  │   ├── sass.js
  │   └── vue.js
  ├── production.js
  ├── shared.js
  └── test.js
```

### ハマりポイント
上記のコマンドを実行すると以下のようなエラーがでる。   

```
Webpack binstubs not found.
Make sure the bin directory or binstubs are not included in .gitignore
Exiting!
```


要はWebpackをインストールしろということらしいので、以下を実行してやる。   
   
```
bundle exec rails webpacker:install
```

これでVueもインストール完了！！  
```
bundle exec rails webpacker:install:vue
```


## foreman
foreman は、Procfile を読み込み、複数のプロセスを管理できるツール。  
今回はRailsの起動とwebpackの起動を同時に行い開発を進めたいので導入する。  

### 導入

`Gemfile`  

```Gemfile
# development内
gem 'foreman'
```

```
$ bundle install
```

### Procfile
アプリケーションを管理するプロセスをProcfileに書き、アプリケーションルートに配置する。  
`Rails`と`Webpack`なのでそれぞれの起動コマンドを書いていく。  

`Procfile`   

```Procfile
web: bundle exec rails s -p $PORT # デフォルトのポートが5000になる
webpacker: ./bin/webpack-dev-server # webpack
```

### 確認と起動

```
## Procfileが有効か確かめる
$ bunlde exec foreman check

## 起動
$ bundle exec foreman start
```

### その他

.foremanにデフォルトのコマンドオプションは仕込んでおける。    
foremanからの起動だとデフォルトで5000が使用されるため、Railsで慣れ親しんだ`3000`からアクセスできるように`.foreman`ファイルに以下を追記する。    

```
port: 3000
```


## Hello Vueを表示

準備ができたので早速`Hello Vue`を表示してみる。  
Vueをインストールした時に`hello_vue.js`というファイル(bodyタグ内に`Hello Vue`と表示するスクリプト)も同時に作られているので、これを使ってテストして見る。  

やりたいことは、`root`にアクセスして`Hello Vue`を確認する。   

### controller
`home_controller`を作り、`index`メソッド定義する。  

```
bundle exec rails g controller home index
```

### view
`home/index.html.erb`に以下を追記  

```
<%= javascript_pack_tag 'hello_vue' %>
```

### route
`routes.rb`に以下を追記  
```
root 'home#index`
```

`http://localhost:3000`にアクセスすれば`Hello Vue`が表示されるはず！  


## Vuejsを書いて見る
せっかく環境を準備したので簡単なvueを書いて見る  
(ちなみにVueのチュートリアルは1/3程度進んでる。。。)  

`/app/javascripts/pack/`に`index.js`を作成  

```
import Vue from 'vue'

var app = new Vue({
  el: '#vue-app',
  data: {
    id: 1,
    title: 'Hello Vuejs',
    message: '<p>Ruby on Rails</p>'
  }
})
```

`app/views/home/index.html.erb`で使えるように修正する。    

```
<div id="vue-app">
  <h1 v-bind:id="'app-' + id">{{ title }}</h1>
  <div v-html="message"></div>
</div>


<%= javascript_pack_tag 'hello_vue' %>
<%= javascript_pack_tag 'index' %>
```


これで同じように`localhost:3000`へアクセスすると。。。  


## 今回ハマったところポイント
```
You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.
```
というエラーが発生  
aliasを設定してやる必要があるらしいので以下のように追記  

`webpack/shared.js`  

```
  resolve: {
    // aliasを追加する
    alias: { 
      'vue$': 'vue/dist/vue.esm.js'
    },
    
  }
```

これで問題なく表示される.    

## 参考
- [rails/webpacker - github](https://github.com/rails/webpacker)
- [foreman で アプリケーションを動かす。- qiita](http://qiita.com/7kaji/items/6a59977d2ad85604e7fd)
