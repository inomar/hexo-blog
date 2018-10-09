---
title: 【Ruby】hashの配列をhashのvalueでソートする
date: 2017-06-07 17:44:56
tags: ['tech-log','Ruby']
---
今日は`sort_by`に結構時間を取られてしまった。

## やりたいこと
hashの配列をhashのvalueでソートしたい

例えば以下のようなhashの配列があったとする。
これをhashのidを基準にソートしたい

```
a = [
  {
    id: 1,
    name: 'foo'
  },
  { 
    id: 3,
    name: 'bar'
  },
  { 
    id: 2,
    name: 'baz'
  }
]

# idを基準にソートしたい
# 結論
p a
p a.sort_by{ | val | val[:id] }

# => [{:id=>1, :name=>"foo"}, {:id=>3, :name=>"bar"}, {:id=>2, :name=>"baz"}]
# => [{:id=>1, :name=>"foo"}, {:id=>2, :name=>"baz"}, {:id=>3, :name=>"bar"}]
```

`sort_by`メッソドを使い、ブロック引数を設定してやることでできた！


