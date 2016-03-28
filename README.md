# Deploy on Heroku

[![Heroku Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/kasei-san/relation_form_sample)

# Environments

```
AWS_ACCESS_KEY
AWS_SECRET_ACCESS_KEY
S3_BUCKET
AWS_REGION
AWS_HOST_NAME
```

- see : [README_3rd_iteration.md](README_3rd_iteration.md)

# Setup

```
bundle install --binstubs=bundle_bin --without production
rake db:migrate && rake db:seed
```
# Iteration

- 1フォームで1対多のモデルを編集可能にしてみる : [README_1st_iteration.md](README_1st_iteration.md)
- 写真をupする仕組みを作る : [README_2nd_iteration.md](README_2nd_iteration.md)
- 写真をS3にupする : [README_3rd_iteration.md](README_3rd_iteration.md)
