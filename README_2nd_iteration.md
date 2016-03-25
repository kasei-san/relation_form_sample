Paperclip を使って Rails アプリに画像アップロード機能を追加する

# やりたいこと

Rails アプリに画像アップロード機能を追加したい

# 方法

[thoughtbot/paperclip](https://github.com/thoughtbot/paperclip) を使う

## paperclip のインストール

Gemfile

```
# 次のイテレーションで aws-sdk を使うが、その時に ver2 を使いたいので、Paperclipを最新のものにした
gem 'paperclip', git: 'https://github.com/thoughtbot/paperclip', tag: 'v5.0.0.beta1'
```

## Photo model に、paperclip の設定を追加

app/models/photo.rb

```ruby
class Photo < ActiveRecord::Base
  belongs_to :item
  has_attached_file :image, styles: { medium: "300x300>", thumb: "100x100>" }, default_url: "/images/:style/no_image.png"
  validates_attachment_content_type :image, content_type: /\Aimage\/.*\Z/
end
```

### Paperclip::ClassMethods#has_attached_file

```ruby
has_attached_file( attachment_name, options={} )
```

- attachment_name : 添付ファイルを参照する時に使用する名前。
    - 今回ならば、 `photo.image_file_name` のように、 attachment_name に紐付いた属性が追加される
- options :
    - styles : オリジナルと別に、`#{name}: #{size}` で指定したサイズの画像を生成する
        - サムネイル等を作成したい時に
        - `photo.image.url(:thumb)` みたいに参照できる
        - 詳しくはこちらを参照 : [PaperClipのstyleで指定する記号の意味 - Qiita](http://qiita.com/zakihaya/items/8b6f5510871b49870559)
   - default_url : 画像がない場合に表示する画像のPATH

参考 : https://github.com/thoughtbot/paperclip#usage

## migrate で、Photo に paperclip 用のカラムを追加

```
rails g migration AddImageColumnsToPhotos
```

```ruby
class AddImageColumnsToPhotos < ActiveRecord::Migration
  def up
    add_attachment :photos, :image
  end

  def down
    remove_attachment :photos, :image
  end
end
```

## View

app/views/items/show.html.haml

```
%ul
  - @item.photos.each do |photo|
    %li
      = photo.title
    %li
      = image_tag photo.image.url(:thumb)
```

## Form

### params[:photo] を許可

app/controllers/items_controller.rb

```ruby
    def item_params
      params.require(:item).permit(:name, :price, photos_attributes: %i[id title image _destroy])
    end
```

### form に file_field を追加

app/views/items/_photo_fields.haml

```ruby
.nested-fields
  .field
    = f.label :title
    %br
    = f.text_field :title
    = f.file_field :image
  = link_to_remove_association 'remove', f
```

これで、`public/system` に写真がupされるようになる

```
.
└── photos
    └── images
        └── 000
            └── 000
                └── 001
                    ├── medium
                    │   └── IMG_8990.JPG
                    ├── original
                    │   └── IMG_8990.JPG
                    └── thumb
                        └── IMG_8990.JPG
```
