1フォームで1対多のモデルを編集可能にしてみる

# Deploy on Heroku

[![Heroku Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/kasei-san/relation_form_sample)

# Item の scaffold を作成

```
rails g scaffold item name:string price:integer
```

# Item の子要素。Photo の scaffold を作成

```
rails g scaffold photo title:string --skip
```

## Item と、Photo をリレーション

app/models/Photo.rb

```ruby
class Photo < ActiveRecord::Base
  belongs_to :item
end
```

app/models/item.rb

```ruby
class Item < ActiveRecord::Base
  has_many :photos, dependent: :destroy
end
```

db/seeds.rb

```ruby
1.upto(10) do |i|
  item = Item.create(name: "item_#{i}", price: i*100)
  1.upto(5) do |j|
    item.photos << Photo.create(title: "#{i}_#{j}")
  end
end
```

# ここで急に haml で書きたくなった

Gemfile

```
gem 'haml-rails'
gem 'erb2haml'
```

```
bundle install
```

一括変換!

```
rake haml:replace_erbs
```

# Item と Photo を連携させる

app/controllers/items_controller.rb

```ruby
    def set_item
      @item = Item.includes(:photos).find(params[:id])
    end
```

app/views/items/show.html.haml

```haml
%p
  %strong Name:
  = @item.name
%p
  %strong Price:
  = @item.price
%ul
  - @item.photos.each do |photo|
    %li
      = photo.title
```

# Item の form で photo を更新可能にする

- [nathanvda/cocoon: Dynamic nested forms using jQuery made easy; works with formtastic, simple_form or default forms](https://github.com/nathanvda/cocoon) を使う

##  Gemfile

```Gemfile
gem 'cocoon'
```

## 動的な追加/削除のための js ライブラリを追加

app/assets/javascripts/application.js

```javascript
//= require cocoon
```

## cocoonが使うパラメータを許可(_destroy は削除用)

app/views/controllers/items_controller.rb

```ruby
     def item_params
       params.require(:item).permit(:name, :price, photos_attributes: %i[id title _destroy])
     end
```

## 子要素の追加/編集/削除を許可

app/models/item.rb


```ruby
     def item_params
       params.require(:item).permit(:name, :price, photos_attributes: %i[id title _destroy])
     end
```

## 子要素の追加/編集/削除を許可

app/models/item.rb

```ruby
accepts_nested_attributes_for :photos, reject_if: :all_blank, allow_destroy: true
```

## 子要素を編集するフォームを追加

app/views/items/_form.html.haml

```haml
 %h3 Photos
 #photos
   = f.fields_for :photos do |photo|
     = render 'photo_fields', f: photo
   .links
     = link_to_add_association 'add photo', f, :photos```
```

app/views/items/_photo_fields.haml

```haml
.nested-fields
  .field
    = f.label :title
    %br
    = f.text_field :title
  = link_to_remove_association 'remove', f
```

# 参考

- [Rails4で1対多のリレーションをモデルに実装する - Rails Webook](http://ruby-rails.hatenadiary.com/entry/20141203/1417601540)
- [3. 1対多の関連を持つオブジェクトを編集可能なフォーム — Railsアプリケーション構築ガイド](http://rails.densan-labs.net/form/relation_register_form.html)
