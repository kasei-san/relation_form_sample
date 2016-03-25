# Deploy on Heroku

[![Heroku Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/kasei-san/relation_form_sample)

# Setup

```
bundle install --binstubs=bundle_bin --without production
rake db:migrate && rake db:seed
```
# Iteration

- 1フォームで1対多のモデルを編集可能にしてみる : [README_1st_iteration.md](README_1st_iteration.md)
- 写真をupする仕組みを作る : [README_2nd_iteration.md](README_2nd_iteration.md)
