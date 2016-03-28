paperclip の写真の格納先をS3に変更する

# やりたいこと

paperclip の写真の格納先をS3にしたい

# 方法

Paperclip に S3 用のオプションがあるので、それを使う

## aws-sdk のインストール

Gemfile

```
gem 'aws-sdk'
```

## paperclip のデフォルトの設定を追加

config/application.rb

```
  class Application < Rails::Application
    config.active_record.raise_in_transactional_callbacks = true

    config.paperclip_defaults = {
      storage:        :s3,
      bucket:         ENV['S3_BUCKET'],
      s3_region:      ENV['AWS_REGION'],
      s3_credentials: {
        access_key_id:     ENV['AWS_ACCESS_KEY'],
        secret_access_key: ENV['AWS_SECRET_ACCESS_KEY']
      },
      hash_secret:    'uO4l0IJA5hWKPo3kDgXj/Oo9H1KvDt912jDkzOIpP2ecaBMQgejN0MtVdkCee0FMOooNAnqIyMzIbb9BINVMbVza8Xbu8LT7Ee1vu538JzRtUKpLlB6hOezs/FzCBSmmmch4pDUemROm7iS8O8GAKs9jNkLUJydbqXHBvWX2qJM',
      path:           ':attachment/:style/:hash.:extension',
    }
  end
end
```

- storage: S3を使いたい場合は `:s3`
- bucket: 格納先のbucket名
- s3_credentials: S3の認証情報
    - `access_key_id` と、`secret_access_key` が必要
- s3_region: S3のリージョン
    - S3の管理画面にアクセスした時に `home?region=us-east-1` みたいに URLに表示されるやつを指定すればよい...はず...
- s3_host_name: S3のホスト名
    - S3の管理画面の適当なオブジェクトのプロパティを開いて「リンク」を見れば分かる
- hash_secret: ファイル名にhash(後述)を使いたい場合のhashのキー
    - secret とあるけど、まぁ別にhash名から元のファイル名を復元されても困らないので直書き
- path: S3のup先のpath
    - attachment: `has_attached_file` で指定した `attachment_name`
    - style: `has_attached_file` で指定した `style`
        - medium とか thumb とか。後オリジナル画像は、original
    - hash: ファイル名をhashにしたもの
        - ファイル名が2バイト文字とかの時に困らないように
    - extension: ファイルの拡張子

参考 : https://github.com/thoughtbot/paperclip#defaults

# トラブルシューティング

## endpoint を指定しろ的なエラーが出る

```
The bucket you are attempting to access must be addressed using the specified endpoint. Please send all future requests to this endpoint
```

- s3_host_name の設定漏れ

## 環境変数 `AWS_REGION` を設定しろ的なエラーが出て、設定してもおんなじエラーが出る

```
Aws::Errors::MissingRegionError (missing region; use :region option or export region name to ENV['AWS_REGION']):
```

- s3_region を設定する
- 環境変数を設定しても paperclip の s3_region を優先するらしく、反映されない






