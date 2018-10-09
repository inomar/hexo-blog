---
title: Rubyで文字列からURLを抽出する
date: 2018-09-12 17:10:17
tags:
---

とある案件で某求人サイトに掲載している企業情報をスクレイピングしてほしいというものがあり、受けたときの話です。

要件としては企業名や事業内容はもちろんのことできればその企業にコンタクトできる要素(企業サイトのURLやメールアドレスなど)がほしいというものでした。

サイト自体のスクレイピングは特に対策もされていなかったので難易度は高くなかったのですが、掲載フォーマットが企業毎にバラバラでホームページという項目で`サイトURL`を載せている企業もあれば、お問い合わせ先というような項目でメールアドレスや電話番号などをまとめて掲載している企業もありました。
(以下のような感じ）

> ◯ お問い合わせ
> URL: http://hogehoge.co.jp
> メール: hogehoge@hogehoge.co.jp
> 気軽にご連絡ください。

`ホームページ`項目を設けている場合はその項目を抜き出せばいいのですが、上記のような文中にURLが紛れている場合は少し厄介でした。

今回は文章中にあるURLを抜き出すやり方を調べたのでその備忘録を書きます。

スクレイピングはRubyでおこなっているのでコードはRubyです。(本当はPythonのほうがいいのだろうけど)

## 結論
`URI.extract`  を使うだけです。簡単でした。
[singleton method URI.extract (Ruby 2.5.0)](https://docs.ruby-lang.org/ja/latest/method/URI/s/extract.html)

```
require 'uri'

str = "
    ◯ お問い合わせ
    URL: http://hogehoge.co.jp
    メール: hogehoge@hogehoge.co.jp
    気軽にご連絡ください。
"
urls = URI.extrct(str)
pus urls # => ["http://hogehoge.co.jp"]
```


`URI.extract` を使うことで文字列からURLのみを抜き出すことができました。

また第２引数にスキーマを配列で指定することで指定したスキーマのURIを取得することができます。

```
URI.extract(str, ["http", "https"]) 
```



## おまけ
今回は文中のURLを抽出するのが目的でしたが、Railsでもvalidationで使ったことがあるので載せておきます。
例えばお問い合わせフォームでよくあるのが本文中にURLを含めさせたくない場合に以下のような例を使うといいかもです。


```
validate :include_url_validation

def include_url_validation
    urls = URI.extract(message, ['http', 'https'])
    errors.add(:message, 'にはURLは含めないでください') unless urls.empty?
end
```


おしまい。。
