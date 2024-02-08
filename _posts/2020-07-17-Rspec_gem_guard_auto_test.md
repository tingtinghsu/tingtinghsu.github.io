---
title:  "使用guard自動跑Rspec測試"
preview: "use guard gem to do Rspec auto testing"
permalink: "/articles/2020-07-17-Rspec_gem_guard_auto_test"
date:   2020-07-17 03:23:00
layout: post
tags: 
  - "rubygem"  
---


最近開始撰寫測試，我使用的套件是Rspec，每次測試時免不了都要打`Rspec`指令。
但是工程師就是要開始培養`懶惰`的美德，
經過大大介紹後，發現可以使用[guard](https://github.com/guard/guard)這個ruby套件，幫我們每週省下好多時間。

此篇文章的目的是想補充文件內容不足的部分，讓大家更容易安裝及使用guard！

<!-- more -->

# Step 1. 在檔案夾內新增`Gemfile`

大部分網路上的解說都是把guard用於跑Rails專案的測試，
但其實一般只想測試自己的ruby code也可以唷！

首先，在你想要進行測試的資料夾新增一個`Gemfile`

```ruby
  source 'https://rubygems.org' 
  gem 'rspec'
  gem 'guard'
  gem 'guard-rspec', '4.7.3'
```
我們需要安裝的gem有`rspec`、`guard`和`guard-rspec`。
寫完Gemfile之後，`bundle install`更新套件相依性

# Step 2. `bundle exec guard init`

此步驟是讓`guard`能夠產生一個`Guardfile`的ruby檔。

接著我們來編輯 `Guardfile`：
guard watch 的路徑需要配合自己的需求做修改
例如：我的測試資料夾的結構如下（使用`tree`套件查詢）：

```
 master ●  tree
.
├── Gemfile
├── Gemfile.lock
├── Guardfile
├── atm.rb
├── spec
│   └── atm_spec.rb
└── tmp

```

一般慣例會把放測試資料的檔案夾命名為`spec`，在`guard`裡`watch`的spec檔案路徑就是`^spec/.+_spec.rb`

```ruby
guard :rspec, cmd: "bundle exec rspec" do
  watch(%r{^spec/.+_spec.rb$})
  watch(%r{(.+).rb$}) { |m| "spec/#{m[1]}_spec.rb" }
  watch('spec/spec_helper.rb') { "spec" }
end
```

提醒一下，`Rspec`引入需要被測試的檔案時，可以使用相對路徑。

```ruby
require_relative '../atm.rb'

RSpec.describe ATM do
# 實作內容
end
```

# Step 3. `bundle exec guard`

```
master ●  bundle exec guard
11:05:14 - INFO - Guard::RSpec is running
11:05:14 - INFO - Guard is now watching at '/Users/tingtinghsu/Documents/projects/astro_ruby.html/codewar'
```



以後只要更改原始的檔案，`Guard`就會幫我們自動跑測試了，是不是很省事！每天可以提早下班啦～

```
17:21:38 - INFO - Running: spec/atm_spec.rb
......

Finished in 0.00396 seconds (files took 0.17085 seconds to load)
6 examples, 0 failures

```

# 參考資料

[GitHub/guard](https://github.com/guard/guard)  

[ERROR - Could not load 'guard/rspec' or' ' find class Guard::Rspec](https://stackoverflow.com/questions/28046509/error-could-not-load-guard-rspec-or-find-class-guardrspec)  

[Mac 印出樹狀資料夾目錄結構](https://blog.dmoon.tw/print-folder-tree-on-mac/index.html)