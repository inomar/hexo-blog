---
title: Railsで複数のレコードを同時に登録したい
date: 2017-09-19 17:20:59
tags: ['tech-blog', 'Rails']
---


Railsで親子関係のレコードを同時にかつ、複数の子レコードを一度に登録・更新したい場合がある。
そんな時に`nested_attributes_for`と`filds_for`を使うことで上記の内容が実現できたので、ログを残す。

今回はユーザー情報の登録の際に、同時に`name`と`description`をもつナニカのレコードを登録・更新したいと思う。(テスト的に作ったので、ナニカはなんでもいい。ちなみにサンプルコードでは`Profile`modelを定義している。。。)
こちらがサンプル
[forms_rails_sample - github](https://github.com/inomar/forms_rails_sample)

## 環境
- ruby 2.4.0
- rails 5.1.2
- Mac OS X

## 
`User`モデルと`Profile`モデルをそれぞれ作っておく

```
# user
$ bundle exec rails g model User name age:integer

# profiel
$ bundle exec rails g model Profile name description:text
```

userとprofileは1対多の関係

`profile.rb`  

```ruby
class Profile < ApplicationRecord
	belongs_to :user
end
```

`user.rb`
```ruby
class User < ApplicationRecord
	has_many :profiles
	accepts_nested_attributes_for :profiles, allow_destroy: true
end
```

`User`モデルでは`accepts_nested_attributes_for :profiles`を追記しておく`allow_destroy: true`は子単位で削除する時に設定が必要
これを追記することで、userにネスとしたprofileを作成・更新・削除できる。

`user_controller.rb`  

```
def new
  @user = User.new
  @user.profiles.build
end

def create
	@user = User.new user_param
	@user.save
	redirect_to users_path
end

private

def user_param
		params.require(:user).permit(:name, :age, profiles_attributes: [:name, :description, :id, :_destroy])
end
```

`_form.html.erb`  

```
<%= form_for @user do |f| %>
<div class="form-group">
  <%= f.label :name, 'user name' %>
  <%= f.text_field :name, class: 'form-control' %>
</div>
<div class="form-group">
  <%= f.label :age %>
  <%= f.text_field :age, class: 'form-control' %>
</div>
<hr>
<%= f.fields_for :profiles do |p| %>
<div class="form-group">
  <%= p.label :name, 'title' %>
  <%= p.text_field :name, class: 'form-control'%>
</div>
<div class="form-group">
  <%= p.label :description %>
  <%= p.text_area :description, class: 'form-control'%>
</div>
  <% if @user.persisted? %>
    <%= p.check_box :_destroy %>
    <%= p.label :_destroy, '削除' %>
  <% end %>
<% end %>


<%= f.submit '作成' %>
<% end %>
```

結構考えたけど、簡単にできたー


### 参考
[Railsでaccepts_nested_attributes_forとfields_forを使ってhas_many関連の子レコードを作成/更新するフォームを作成 - Rails Webook](http://ruby-rails.hatenadiary.com/entry/20141208/1418018874)


