---
title: 使用Vagrant建立Rails開發環境
date: 2013-09-26 22:10
tags: ruby,rails,vagrant
---

目標: 建立Rails的Virtual Box VM

####安裝Vagrant
[Vagrant Installer](http://downloads.vagrantup.com/)

#### Setup Vagrant Box

~~~
$ vagrant init centos-6.3 https://dl.dropbox.com/u/7225008/Vagrant/CentOS-6.3-x86_64-minimal.box
~~~

READMORE

VM需要Box來初始化，[Vagrantvbox.es](http://www.vagrantbox.es/)裡有不少Box檔提供下載，選擇[CentOS 6.3 x86_64 minimal](https://dl.dropbox.com/u/7225008/Vagrant/CentOS-6.3-x86_64-minimal.box)

#### 設定 Vagrantfile
使用`Chef Solo` provisioning，先抓取所需cookbooks，[Opscode community](http://community.opscode.com/)可以找到前人寫好的cookbooks。

接下來就是把這些cookbooks下載下來，但有更好的作法，[Librarian-Chef](https://github.com/applicationsonline/librarian-chef)可用來管理cookbooks。

~~~
$ librarian-chef init
~~~

產生出Cheffile

~~~
#!/usr/bin/env ruby
#^syntax detection

site 'http://community.opscode.com/api/v1'

cookbook 'build-essential'
cookbook 'ruby_build'
cookbook 'rbenv', :git => 'https://github.com/fnichol/chef-rbenv', :ref => 'v0.7.2'
cookbook 'apache2'
cookbook 'passenger_apache2'
~~~

下載 cookbooks

~~~
$ librarian-chef install
~~~


開始設定Vagrantfile，我們需要安裝

* rbenv
* ruby_build
* build-essential
* apache2
* passenger_apache2

~~~
config.vm.provision :chef_solo do |chef|
  chef.cookbooks_path = "cookbooks"
  chef.add_recipe 'build-essential'
  chef.add_recipe 'ruby_build'
  chef.add_recipe 'rbenv::system'
  chef.add_recipe 'apache2'
  chef.add_recipe 'passenger_apache2'
end
~~~

設定cookbook recipes裡所需的attributes，

* ruby: 1.9.3-p448
* 安裝基本的gems:
    * bundler
    * rake
    * rails: 3.2.14
* passenger: 4.0.18

~~~
chef.json = {
  'rbenv' => {
    'rubies'  => ['1.9.3-p448'],
    'global'  => '1.9.3-p448',
    'gems'    => {
      '1.9.3-p448' => [
        { 'name'    => 'bundler' },
        { 'name'    => 'rake' },
        { 'name'    => 'rails', 'version' => '3.2.14' }
      ]
    }
  },
  'passenger' => {
    'version' => '4.0.18'
  }
}
~~~

完整的Vagrantfile

~~~
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant::Config.run do |config|
  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "centos"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "https://dl.dropbox.com/u/7225008/Vagrant/CentOS-6.3-x86_64-minimal.box"

  # Enable provisioning with chef solo, specifying a cookbooks path, roles
  # path, and data_bags path (all relative to this Vagrantfile), and adding
  # some recipes and/or roles.
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = "cookbooks"
    chef.add_recipe 'build-essential'
    chef.add_recipe 'ruby_build'
    chef.add_recipe 'rbenv::system'
    chef.add_recipe 'apache2'
    chef.add_recipe 'passenger_apache2'

    chef.json = {
      'rbenv' => {
        'rubies'  => ['1.9.3-p448'],
        'global'  => '1.9.3-p448',
        'gems'    => {
          '1.9.3-p448' => [
            { 'name'    => 'bundler' },
            { 'name'    => 'rake' },
            { 'name'    => 'rails', 'version' => '3.2.14' }
          ]
        }
      },
      'passenger' => {
        'version' => '4.0.18'
      }
    }
  end
end
~~~

最後啟動vagrant box

~~~
$ vagrant up
~~~

進入vagrant vm

~~~
$ vagrant ssh
~~~

cookbooks新增/修改可以透過`vagrant provision` 更新

#### 其他指令

```
$ vagrant status
$ vagrant suspend
$ vagrant resume
$ vagrant halt - shut down VM
$ vagrant package - package up the virtual machine in its current state into a file called package.box
$ vagrant destroy - delete VM
```

其他更詳細的說明，請參考[Vagrant Documentation](http://docs.vagrantup.com/v2/)
