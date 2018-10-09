---
title: Reactを学んでみる①【環境構築~hello world編】
date: 2017-12-19 17:17:40
tags: ['tech-blog', 'React']
---


前々から興味は合ったんだけど、全然手を出せていなかったReactに挑戦してみたので、その備忘録を書きます。
今回は環境構築編ということで苦手意識が強かったビルドツールについても頑張って挑戦しました。

## Reactとは？
FacebookがOSSとして公開しているUIパーツを作るJavaScriptのライブラリです。
なのでフルスタックなフレームワークであるBackbone.jsやAngular.jsと比較される場合、
React.js + Reduxのセットで考えられるようです。

## 実際にやってみた

最終的な目標としてはよくサンプルとして出回っているような
**入力フォームに値を入れると即座に画面に表示する(双方向バインディング)**
サンプルを作成したいと思います。
[サンプルコード](https://github.com/inomar/sample-react)

今回は環境構築をしてからHelloWorld表示で一区切りつくようと思います。

## 環境構築
利用環境は以下の通りです。
- Mac OSX
- yarn 1.3.2

### yarnの導入

まずは必要なパッケージを`yarn`を使ってインストールしていきます。
`yarn`はFacebookeが開発した`npm`に代表されるようなJSのパッケージマネージャで、`npm`よりも依存パッケージのインストール速度が速い！とのこと。。

という訳でまずは`yarn`をインストールしていきます。
今回は`homebrew`を使ってインストールします。

```
$ brew install yarn
```

`npm`を使ってもインストールできるらいしので一応載せておきます。

```
$ npm install -g yarn
```

`yarn`がインストールできたら`yarn init`で`package.json`を作成します。
`package.json`は、プロジェクトのパッケージを管理するファイルで、このあと`yarn add ~`とパッケージを追加していくたびにこちらのファイルに自動的に反映され、管理していくことになります。

※ `yarn init`を実行すると対話的に設定を行えますが、パッケージを公開するために必要な情報なので、今回はすべてデフォルトで大丈夫です(全部Enter key押す)
 
```
$ yarn init
question name (sample-react): 
question version (1.0.0):
question description:
question entry point (index.js):
question repository url:
question author:
question license (MIT):
question private:
success Saved package.json
Done 
```

### webpack, babelの導入
`package.json`を作成後、以下のコマンドで必要なパッケージをインストールしていきます。

```
$ yarn add webpack webpack-dev-server babel-core babel-loader babel-preset-react babel-preset-es2015
```

`webpack`はアプリケーションに必要なリソースの依存関係を解決し、アセットを生成するビルドツールです。
`webpack-dev-server`でローカル環境での開発を実現しています。
`babel`はes2015,reactで書かれたコードをes5に変換してくれるものです。(ブラウザによってはes2015などに対応していないものがあるので)


### sassの導入
本編とはあまり関係ありませんが、スタイルはSassを使って記述して行こうと思います。

```
$ yarn add node-sass style-loader css-loader sass-loader import-glob-loader extract-text-webpack-plugin
```

### webpack.config.jsを作成
もろもろのインストールが済んだら`webpack`の設定をしていきます。
プロジェクト直下に`webpack.config.js`を作成します。

```webpack.config.js
const path = require('path');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

const publidDir = path.join(__dirname, '/public');
module.exports = [
  {
    entry: [
      './src/index.js',
    ],
    output: {
      path: publidDir,
      publicPath: '/',
      filename: 'bundle.js',
    },
    module: {
      loaders: [{
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['react', 'es2015'],
        },
      }],
    },
    resolve: {
      extensions: ['.js', '.jsx'],
    },
    devServer: {
      historyApiFallback: true,
      contentBase: publidDir,
    },
  },
  {
    entry: {
      style: './stylesheets/index.scss',
    },
    output: {
      path: publidDir,
      publicPath: '/',
      filename: 'bundle.css',
    },
    module: {
      loaders: [
        {
          test: /\.css$/,
          loader: ExtractTextPlugin.extract({ fallback: 'style-loader', use: 'css-loader' }),
        },
        {
          test: /\.scss$/,
          loader: ExtractTextPlugin.extract({ fallback: 'style-loader', use: 'css-loader!sass-loader' }),
        },
      ],
    },
    plugins: [
      new ExtractTextPlugin('bundle.css'),
    ],
  },
];
```

`publidDir`でプトジェクト内の`public`ディレクトリを指定しています。(`public`ディレクトリはまだ作ってません。)
簡単な流れを書くと`entry`に書いた`src/index.js`の内容を`webpack`が依存関係を解決し、ビルドしたものを`output`で指定された`public/bundle.js`という形で吐き出してくれます。下段のSassの場合も同様ですが、`webpack`はjsしか理解できないため、`loader`を使ってjs以外のものも処理してくれています。そのほかの詳細は[webpackの公式ドキュメント](https://webpack.js.org/concepts/)を参照してください。
※ まだ`react`はインストールしていませんが、後ほどインストールするのでその設定も書いておきます。

`webpack.config.js`で書いたようにプロジェクトに`public`、`src`ディレクトリをそれぞれ作成します。
`public`ディレクトリにはHelloWorldを表示するためのindex.htmlを作成し、
`src`ディレクトリにはindex.jsを作成します。
念のためスタイルも当てておきます。
`stylesheets/index.scss`を作成。

`index.html`

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Sample React</title>
    <link rel="stylesheet" href="bundle.css">
  </head>
  <body>
  <div class="container"></div>
  <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

`index.js`

```
alert('Hello World');
```

`index.scss`

```
body {
  background-color: #eee;
}
.container {
  margin: 100px 0;
  text-align: center;
  color: blue;
  font-size: 24px;
}
```

## HelloWorld (webpack-dev-server)

index.jsに書いたalertを実際に表示させる為に`webpack-dev-server`を起動してローカルで確認していきます。
実は`yarn add ~`したタイミングで`node_modules`というディレクトリが作られる。これはインストールしたライブラリやパッケージが実際に格納されているディレクトリで`webpack-dev-server`もこの中にあります。

`webpack-dev-server`の起動コマンドは以下

```
$ ./node_modules/.bin/webpack-dev-server
```

これを実行し、`localhost:8080`をブラウザで叩くことで先ほどのalertが確認できると思う。
ただ毎回`./node_modules/.bin/webpack-dev-server`をターミナルに打ち込むのは面倒なので、`package.json`にscriptとして登録しておき
`yarn run start`という形で起動できるようにしたい。やり方は`package.json`に以下を追記するだけ

```
"scripts": {
  "start": "./node_modules/.bin/webpack-dev-server"
 },
```

これで`yarn run start`で`webpack-dev-server`を起動できると思います。

## Reactを導入

いよいよReactをインストールしていきたいと思います。

```
$ yarn add react react-dom
```

`react`はもちろんReactのインストールをするのですが、Reactのみだとhtmlのdomを扱うことができないため、
`react-dom`も一緒にインストールする必要があります。


## Hello React

細かい文法は次回にして以下のように`index.js`を書き直します。

```
import React from `react`;
import ReactDom from 'react-dom';

ReactDom.render(<div>Hello, React!</div>, document.querySelector('.container'));
```
ターミナルで先ほど`package.json`で設定したスクリプトを実行すると

```
$ yarn run start
```

![hello,react](http://inomar.media.s3.amazonaws.com/blog/image/hello_react_20171221.png)

`Hello React`ができました！

という訳でかなり雑ではありますが、Reactの環境構築からHello Reactまで確認できました。
次回は双方向バインディングまでできるといいな。。。

