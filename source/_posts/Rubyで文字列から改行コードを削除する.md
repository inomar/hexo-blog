---
title: Rubyで文字列から改行コードを削除する
date: 2018-08-27 17:12:12
tags: ['tech-log', 'Ruby']
---

よく忘れてしまいがちなので、備忘録

## 結論

```
str = "\r\nあいうえお\n"
str.gsub(/[\r\n]/,"") # => "あいおうえお"
```

`gsub`は第一引数の正規表現パターンに一致した文字列を第二引数にセットした値に置き換えるメソッド


## 参考
[gsub, gsub! (String) - Rubyリファレンス](https://ref.xaio.jp/ruby/classes/string/gsub)