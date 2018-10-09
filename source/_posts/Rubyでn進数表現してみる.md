---
title: Rubyでn進数表現してみる
date: 2018-06-20 17:15:14
tags: ['tech-log', 'Ruby']
---

アルゴリズムについて基礎から勉強し直しているんですが、実際にコードを書かないと覚えられないと思い、数年前に買った"プログラマ脳を鍛える数学パズル"を教材に１日1問を解いて、書くこと目標に実施中！(最近新しいのが出たらしい)

今後こちらのブログでそのアウトプットをしていきたいと思います。
流石に問題を載せるのはマズいと思うので、問題を通しての気づきなどをまとめます。

## Rubyでn進数

普段10進数しか扱わないので忘れがちですが、Rubyで2進数や8進数を扱う場合は以下のようになります。

```
10.to_s(2) # => "1010"
10.to_s(8) # => "12"
```

ご覧の通り`to_s`メソッドの引数に基数を指定してあげることで、文字列の2進数、8進数を表現することが可能です。
逆に2進数、8進数を10進数にしたい場合は`to_i`メソッドの引数に基数を指定することで表現が可能です。

```
"1010".to_i(2) # => 10
"12".to_i(8) # => 10
```





<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=FFFFFF&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=inomar14-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=479814245X&linkId=82079bb70dbb75da066a187fe25067d3"></iframe>

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=FFFFFF&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=inomar14-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4798153613&linkId=620850aed66e8f0d73a195e9a77fc68e"></iframe>
