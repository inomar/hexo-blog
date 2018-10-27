---
title: learning-react
tags:
---

### babel/preset-env

```bash
$ yarn add --dev @babel/preset-env
```
@babel/preset-envは自動的にトランスパイルをおこなてくれるモジュール
ターゲットブラウザーも指定できる(古いブラウザに対応するとファイルサイズも大きくなるため)
以下設定(Opera miniを除いた0.25%より大きいブラウザシェアを持つブラウザに対応)

```.babelrc
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": "> 0.25%, not op_mini all"
      }
    ]
  ]
}
```

### webpack

```bash
$ yarn add --dev webpack webpack-cli @babel/core babel-loader
```

```webpack.config.js
const path = require('path')

const src = path.join(__dirname, 'src');
const dist = path.join(__dirname, 'dist');

module.exports = {
  // developmentモードで実行 => ? TODO
  mode: 'development',
  entry: path.resolve(src, 'js/index.js'),
  output: {
    // 生成されるファイル名
    filename: 'indexbundle.js',
    // 生成先ディレクトリ
    path: dist
  },
  resolve: {
    // import分のパスしていにnode_modulesを省略できるようにする
    modules: ['node_modules'],
    // .js, .jsxの拡張子を省略できるようにする
    extensions: ['.js', '.jsx'],
  },
  module: {
    rules: [
      {
        // ルールを定期要するファイルの正規表現
        test: /\.(js|jsx)$/,

        // node_modules以下のファイルには適用しない
        exclude: /node_modules/,

        // 使用するloader
        loader: 'babel-loader'
      }
    ]
  },
  // sourceMappingの設定 => ? TODO
  devtool: 'cheap-module-eval-source-map'
}

```

```package.json
// 追加
"scripts": {
  "build:dev": "webpack --config webpack.config.js"
}
```

```bash
$ yarn build:dev
```

### webpack-dev-server

```bash
$ yarn add --dev webpack-dev-server html-webpack-plugin
```

```webpack.config.js
const path = require('path')
// webpackモジュールを読み込み
const webpack = require('webpack');

// html-webpack-pluginモジュールを読み込む
const htmlWebpackPlugin = require('html-webpack-plugin');

const src = path.join(__dirname, 'src');
const dist = path.join(__dirname, 'dist');

module.exports = {
  // developmentモードで実行
  mode: 'development',
  devServer: {
    contentBase: dist, // 開発サーバーを立ち上げる参照ディレクトリ
    hot: true, // hot-reloadを有効
    port: 4000 // サーバーを立ち上げるポート

  },
  entry: path.resolve(src, 'js/index.js'),
  output: {
    // 生成されるファイル名
    filename: 'indexbundle.js',
    // 生成先ディレクトリ
    path: dist
  },
  resolve: {
    // import分のパスしていにnode_modulesを省略できるようにする
    modules: ['node_modules'],
    // .js, .jsxの拡張子を省略できるようにする
    extensions: ['.js', '.jsx'],
  },
  module: {
    rules: [
      {
        // ルールを定期要するファイルの正規表現
        test: /\.(js|jsx)$/,

        // node_modules以下のファイルには適用しない
        exclude: /node_modules/,

        // 使用するloader
        loader: 'babel-loader'
      }
    ]
  },
  plugins: [
    // hot-reloadを有効にするプラグインを追加
    new webpack.HotModuleReplacementPlugin(),

    // HtmlWebpackPluginプラグインを追加
    new htmlWebpackPlugin()
  ],
  // sourceMappingの設定
  devtool: 'cheap-module-eval-source-map'
}
```

```package.json
// scriptsに追加
"server": "webpack-dev-server --open --config webpack.config.js"
```

```bash
$ yarn server
```

### ESLint

ESLintの依存関係を確認
```bash
$ yarn info eslint-config-airbnb peerDependencies

yarn info v1.7.0
{ eslint: '^4.19.1 || ^5.3.0',
  'eslint-plugin-import': '^2.14.0',
  'eslint-plugin-jsx-a11y': '^6.1.1',
  'eslint-plugin-react': '^7.11.0' }
✨  Done in 0.16s.

```

```bash
$ yarn add --dev eslint-config-airbnb eslint@^5.3.0 eslint-plugin-import@^2.14.0 eslint-plugin-jsx-a11y@^6.1.1 eslint-plugin-react@^7.11.0 babel-eslint
```


```.eslintrc
{
  "extends": ["airbnb"], # airbnbのルールを継承
  "env": {
    "browser": true, # ブラウザのグローバル変数を有効化
    "es6": true # es6の構文を有効化
  },
  "parser": "babel-eslint", # babel-eslintをパーサとして使用
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true # jsxを有効
    }
  }
}
```

```pacakge.json
# scriptsに追加
"lint:js": "eslint './src/js/*.{js,jsx}'"
```

#### build毎にeslintを実行

```bash
$ yarn add --dev eslint-loader
```

```webpack.config.js
{
  module: {
    roles [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_,modules/,
        enforce: 'pre', // babel-loaderよりも前に実行される
        loader: 'eslint-loader'
      },
      ...
    ]
  }
}

```

```bash
$ yarn server
```

エラーがある場合はコンパイルエラーになる

### Prettier
Prettierはコードフォーマッタ(js,jsx,css)

```bash
$ yarn add --dev prettier
```

```.prettierrc
{
  "printWidth": 100, # 1行あたりの最大文字数
  "singleQuote": true # ダブルクウォート->シングル
}
```

```package.json
# scriptsに追加
+ "prettier": "prettier --write './src/js/*.{js,jsx}'"
```

### Flow
静的型チェックツール
- コードの品質を上げる
```bash
$ yarn add --dev flow-bin @babel/preset-flow eslint-plugin-flowtype
```

flowはトランスパイルする必要があるため.babelrcで以下を追加
```.babelrc
# presets[]に追加
"@babel/preset-flow"
```

FlowはESLintと競合する場合があるため、Flow用のESLintプラグインを導入
```.eslintrc
"extends": ["airbnb",
    "plugin:flowtype/recommended"
    ], # airbnbのルールを継承
  "plugins": ["flowtype"],
```

```.flowconfg
[ignore]
# Flowの対象外ファイルパスを記述

[include]
# ルート以外のFlowの対象ファイルパスを記述

[libs]
# 外部のFlow定義などのファイルパスを記述

[options]
# オプションの定義を記述
```

```package.json
# scriptsに追加
"flow": "flow"
```

対象のファイルにコメントでflowを使うことを宣言
```index.js
// @flow
export class Hello {
  name: string;
  constructor(name: string) {
    this.name = name;
    this.say();
  }

  say() {
    console.log(`Hello ${this.name} world!`);
  }
}

export default new Hello('inomar');
```

- boolean
- string
- number
- null
- void(undifined)
- ?(MaybeType): null or void
(?stringはstring,null,voidのいずれか) => 変数や引数がnullかundifinedになる可能性がある場合に活躍


## Part4

```bash
$ yarn add react react-dom
$ yarn add --dev @babel/preset-react @babel/core
```

.babelrcにprest-reactを追記
```.babelrc
"@babel/preset-react",
```


## Part5
### cssを適用

```bash
$ yarn add --dev mini-css-extract-plugin css-loader
```

- css-loader: javascript上でcssファイルをimport
- mini-css-extract-plugin: cssファイルを作成

webpack.config.jsを変更

```webpack.config.js
const path = require('path');
// webpackモジュールを読み込み
const webpack = require('webpack');

// html-webpack-pluginモジュールを読み込む
const htmlWebpackPlugin = require('html-webpack-plugin');
const miniCSSExtractPlugin = require('mini-css-extract-plugin');


const src = path.join(__dirname, 'src');
const dist = path.join(__dirname, 'dist');

module.exports = {
  // developmentモードで実行
  mode: 'development',
  devServer: {
    contentBase: dist, // 開発サーバーを立ち上げる参照ディレクトリ
    hot: true, // hot-reloadを有効
    port: 4000, // サーバーを立ち上げるポート

  },
  entry: path.resolve(src, 'js/render.jsx'),
  output: {
    // 生成されるファイル名
    filename: 'indexbundle.js',
    // 生成先ディレクトリ
    path: dist,
  },
  resolve: {
    // import分のパスしていにnode_modulesを省略できるようにする
    modules: ['node_modules'],
    // .js, .jsxの拡張子を省略できるようにする
    extensions: ['.js', '.jsx'],
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_,modules/,
        enforce: 'pre', // babel-loaderよりも前に実行される
        loader: 'eslint-loader',
      },
      {
        // ルールを定期要するファイルの正規表現
        test: /\.(js|jsx)$/,

        // node_modules以下のファイルには適用しない
        exclude: /node_modules/,

        // 使用するloader
        loader: 'babel-loader',
      },
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: [miniCSSExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
  plugins: [
    // hot-reloadを有効にするプラグインを追加
    new webpack.HotModuleReplacementPlugin(),

    // HtmlWebpackPluginプラグインを追加
    new htmlWebpackPlugin({
      template: path.resolve(src, 'html/index.html'), // templateの設定を追加
    }),
    new miniCSSExtractPlugin(), // miniCSSExtractPluginを追加
  ],
  // sourceMappingの設定
  devtool: 'cheap-module-eval-source-map',
};

```

あとはcssファイルを作成して、jsファイルからimportするだけ
この設定の場合、msin.cssが生成される。ファイル名を指定したい場合は以下のように
filenameを指定する

```
new miniCSSExtractPlugin({
  filename: 'app.css' // ファイル名を指定
})
```

### StyleLint

```bash
$ yarn add --dev stylelint stylelint-config-standard
```

```.stylelintrc
{
  "extends": "stylelint-config-standard"
}
```

```package.json
"scripts": {
  "lint:css": "stylelint './src/css/*.css'"
}
```

### PostCSS
PostCSSはパーサーであり、プラグインで拡張する必要がある

```bash
$ yarn add --dev postcss-loader
```


postcss.config.jsを作成
ここにプラグインを追加していく
```postcss.config.js
module.exports = {
  plugins: {}
}
```

webpack.config.jsにpostcss-loaderの設定を記述
```webpack.config.js

{
  test: /\.css$/,
  exclude: /node_modules/,
  use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader']
}
```

```bash
$ yarn server
```

### postcssのpluginの追加
- autoprefixer: ベンダープレフィックスを自動的に設定してくれる
- postcss-custom-properties: CSS CUSTOM PROPERTIESを解釈するためのプラグイン
- postcss-nesting: ネスト記法を使えるプラグイン

```bash
$ yarn add --dev autoprefixer postcss-custom-properties postcss-nesting
```


```postcss.config.js
plugins: {
    autoprefixer: {
      browsers: ['> 0.25%', 'not op_mini all']
    },
    'postcss-custom-properties': {},
    'postcss-nesting': {},
  },
```
