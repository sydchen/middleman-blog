---
title: Middleman 安裝 & 設定
date: 2013-10-05 20:07
tags: web development,middleman
---

#### 安裝Ruby
如果系統已經有Ruby， 一行就搞定，如果沒有可以參考安裝RVM(Ruby Version Manager)再安裝Ruby。

Linux系統可以透過下面指令安裝RVM

```
$ curl -L https://get.rvm.io | bash -s stable
```

或是更簡單的方式，安裝[RailsInstaller](http://railsinstaller.org/)，支援OS X 和Windows。

#### 安裝Middleman

```
$ gem install middleman
```

開始第一個Middleman Project，養成好習慣先看Help

```
$ middleman init --help

Usage:
  middleman init NAME [options]

Options:
  -T, [--template=TEMPLATE]      # Use a project template: default, html5, mobile, smacss, emp
ty, blog
                                 # Default: default
      [--css-dir=CSS_DIR]        # The path to the css files
      [--js-dir=JS_DIR]          # The path to the javascript files
      [--images-dir=IMAGES_DIR]  # The path to the image files
      [--rack]                   # Include a config.ru file
      [--skip-gemfile]           # Don't create a Gemfile
      [--skip-bundle]            # Don't run bundle install
      [--skip-git]               # Skip Git ignores and keeps
```

Init

```
$ middleman init first_middleman --skip-bundle
```

產生出

```
create  first_middleman/Gemfile
create  first_middleman/.gitignore
create  first_middleman/config.rb
create  first_middleman/source/index.html.erb
create  first_middleman/source/layouts/layout.erb
create  first_middleman/source/stylesheets
create  first_middleman/source/stylesheets/all.css
create  first_middleman/source/stylesheets/normalize.css
create  first_middleman/source/javascripts
create  first_middleman/source/javascripts/all.js
create  first_middleman/source/images
create  first_middleman/source/images/background.png
create  first_middleman/source/images/middleman.png
```

設定`Gemfile`，可以看到除了`middleman`外還裝了`middleman-livereload`。

```
# If you have OpenSSL installed, we recommend updating
# the following line to use "https"
source 'http://rubygems.org'

gem "middleman", "~>3.1.5"

# Live-reloading plugin
gem "middleman-livereload", "~> 3.1.0"
```

加入`redcarpet`(換掉Maruku這個markdown engine)

```
gem "redcarpet"
```

接著跑

```
$ bundle install
```

接著設定最重要的`config.rb`

assets directories

```
set :css_dir, 'stylesheets'
set :js_dir, 'javascripts'
set :images_dir, 'images'
```

開啟livereload

```
activate :livereload
```

Markdown engine換成[Redcarpet](https://github.com/vmg/redcarpet)

```
set :markdown_engine, :redcarpet
set :markdown, :fenced_code_blocks => true, :smartypants => true
```

`config.rb`

```
activate :livereload
set :css_dir, 'stylesheets'
set :js_dir, 'javascripts'
set :images_dir, 'images'
set :markdown_engine, :redcarpet
set :markdown, :fenced_code_blocks => true, :smartypants => true
```

啟動middleman server，預設的port `4567`

```
$ middleman server
```

測試 `http://0.0.0.0:4567`


產生出static files

```
$ middleman build
```

