---
title: Deploy Rails App with Capistrano 3
date: 2013-12-28 21:23
tags: rails, capistrano
---

前陣子用Rails + [jQuery Mobile](http://jquerymobile.com/) 寫了個背單字的網頁，
想說搭車時沒事可以拿手機出來背一下單字，網站弄好後，加個自動部屬，順便練習怎麼用[Capistrano](http://www.capistranorb.com/)。

#### Installation
參考github page上面的指令

`Gemfile`

```
gem 'capistrano', '~> 3.0.1'
gem 'capistrano-bundler'
gem 'capistrano-rails', '~> 1.1.0'
```

`capistrano-bundler`在Server端程式碼更新後跑bundler，`capistrano-rails`則是跑migrations 和 assets compile，但我們只會用到migrations這部分，因為capistrano-rails並沒有支持*assets precompile*，直接在Server跑，但大多數情況應該是先在本機端先跑precompile才是。

#### Initialize Capistrano
執行

```
$ bundle install
```

產生下面檔案

```
├── Capfile
├── config
│   ├── deploy
│   │   ├── production.rb
│   │   └── staging.rb
│   └── deploy.rb
└── lib
    └── capistrano
            └── tasks
```

如果要產生其他stages可以加**STAGES**指定

```
$ bundle exec cap install STAGES=sandbox,qa,rc,production
```

#### Capfile
capistrano的主要設定檔，視需求加入不同的tasks，這裡我們額外加入
`capistrano/bundler`和`capistrano/rails/migrations`

```
# Load DSL and Setup Up Stages
require 'capistrano/setup'

# Includes default deployment tasks
require 'capistrano/deploy'
require 'capistrano/bundler'
require 'capistrano/rails/migrations'

Dir.glob('lib/capistrano/tasks/*.cap').each { |r| import r }
```

#### Server Configuration
兩種語法

* Simple Role Syntax

```
# Supports bulk-adding hosts to roles, the primary
# server in each group is considered to be the first
# unless any hosts have the primary property set.
role :web, %w{deploy@example.com}
role :app, %w{deploy@example.com}
role :db,  %w{deploy@example.com}
```

* Extended Server Syntax

```
# ======================
# This can be used to drop a more detailed server
# definition into the server list. The second argument
# something that quacks like a hash can be used to set
# extended properties on the server.
server 'example.com', user: 'deploy', roles: %w{web app db}, port: 1234
```

完成的`config/deploy/production.rb`

```
set :stage, :production
server 'example.com', user: 'deploy', roles: %w{web app db}, port: 1234
```

#### Shared Information
共享的設定可放在`config/deploy.rb`

```
set :application, 'my app name'
set :repo_url, 'git@example.com:me/my_repo.git'
set :branch, 'master'
set :deploy_to, '/var/www/my_app'
set :scm, :git
```

#### 權限設定
這裡有兩段要設定，第一段是從local machine到remote server，另一段是remote server需要有權限連到Git repository。

在上面的設定中我們指令了*deploy* 帳號來佈署，所以準備在各個remote servers中把這個帳號開設好

```
root@remote $ adduser deploy
root@remote $ passwd -l deploy
```

產生一組新的rsa key

```
$ ssh-keygen -t rsa -f ~/.ssh/deploy_rsa
```

把private key加入ssh agent

```
$ ssh-add ~/.ssh/deploy_rsa
```

public key copy 到server

```
$ ssh root@remote
root@remote $ su - deploy
deploy@remote $ cd ~
deploy@remote $ mkdir .ssh
deploy@remote $ echo "ssh-rsa jccXJ/JRfGxnkh/8iL........dbfCH/9cDiKa0Dw8XGAo01mU/w== /Users/me/.ssh/jesse_rsa" >> .ssh/authorized_keys
deploy@remote $ chmod 700 .ssh
deploy@remote $ chmod 600 .ssh/authorized_keys
```

本機端測試是否成功

```
$ ssh deploy@remote 'hostname; uptime'
```

再把public key也加到git repository這樣子就行了

#### Asset Precompilation

`Capfile`

新增檔案`lib/capistrano/tasks/precompile_assets.cap`

```
namespace :deploy do
  namespace :assets do
    task :precompile do
      run_locally do
        with rails_env: :production do
          execute :rake, "assets:precompile"
        end
      end
    end

    task :sync => 'public/assets' do
      on roles :app do
        upload!('public/assets', "#{release_path}/public/assets", :recursive => true)
      end
    end
  end
end

before "deploy:starting", "deploy:assets:precompile"
after "deploy:updated", "deploy:assets:sync"
```

這裡出現的DSL語法(**run_locally**、**upload!**)都是來自[SSHKit](https://github.com/leehambley/sshkit)，更多的範例可參考[SSHKit Examples](https://github.com/capistrano/sshkit/blob/master/EXAMPLES.md)。

理解[Capistrano deploy flow](http://www.capistranorb.com/documentation/getting-started/flow/)後，就可以加入**before**和**after** hooks，這裡我們在deploy一開始前就先跑`deploy:assets:precompile`這個task，而在remote server更新之後，跑`deploy:assets:sync`把assets sync過去。

#### 開始佈署

```
$ bundle exec cap production deploy

# 也可指定跑某個task
$ bundle exec cap production deploy:assets:precompile
```
