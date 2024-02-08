---
title:  "第12屆鐵人賽Day 30 Rails專案開發 - 網站部署 + 完賽感言"
preview: ""
permalink: "/articles/2020-10-13"
date:   2020-10-10 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

今天就是網站部署、公開上線的日子了！

不過，因為現在只是最小可行產品而已，還有很多預計規劃的功能還沒完成(例如column component的CRUD)，
所以在deploy之前，我想為使用者設計`onboard data`，為他們先建立一個接近真實的範例。

# 設定User的`onboard data`

我希望User每建立新的Kanban，裡面就會出現六個column，可以視覺化地規劃、拖拉自己做的ticket。
在Rails裡面透過`after_create`這個hook來實現，寫法可能類似像這樣：

kanban.rb
```
class Kanban < ApplicationRecord
  after_create :onboard_data

  def onboard_data
    columns.create(name: "Backlog")
    columns.create(name: "Sprint Backlog")
    columns.create(name: "Working On")
    columns.create(name: "Bugs")
    columns.create(name: "Testing")
    columns.create(name: "Done")    
    //略...可以自行設計
  end
end
```

# 將rails專案部署至Heroku

參考了自己兩年前在IT邦的筆記<[將Ruby on Rails專案部署到Heroku](https://ithelp.ithome.com.tw/articles/10199014)>來進行以下的指令：

- heroku create "我的專案名稱"
- git push heroku master
- heroku run bundle install
- heroku run rails db:migrate

```
░▒▓ /vue_rails  master ▓▒░ heroku create leankanban
Creating ⬢ leankanban... done

░▒▓ /vue_rails  master ▓▒░ git push heroku master
Enumerating objects: 897, done.

remote: -----> Launching...
remote:        Released v6
remote:        https://leankanban.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.

 * [new branch]      master -> master


░▒▓ /vue_rails  master ▓▒░ heroku run bundle install

░▒▓ /vue_rails  master ▓▒░ heroku run rails db:migrate
Running rails db:migrate on ⬢ leankanban... up, run.4198 (Free)

```

網站部署順利上線囉！

![](https://i.imgur.com/kHrx2ai.png)


# 設定Redis環境變數：為`production`環境加上redis to go

等等，我們還有即時互動的功能，所以必須為`redis`準備好環境，不然網站會壞掉。  

為了準備`production`環境，我們要來修改專案內的這個檔案`config/cable.yml`（cable這個英文字是`纜線`的意思，命名取的真好）。

```
development:
  adapter: redis
  url: redis://localhost:6379/1

test:
  adapter: test

production:
  adapter: redis
  url: <%= ENV.fetch("REDISTOGO_URL") %>
  channel_prefix: vue_rails_production
```

接著，利用heroku指令幫我們生成線上版的`redis`資料庫的環境變數：
`heroku addons:add redistogo`

```
░▒▓ ~/Doc/p/pmaster_v/vue_rails  master ▓▒░ heroku addons:add redistogo
Creating redistogo on ⬢ leankanban... free
Created redistogo-this-is-secret as REDISTOGO_URL
Use heroku addons:docs redistogo to view documentation
```

去在Heroku後台的`settings/Config Var`，放環境變數的地方檢查是否成功設定：

![](https://i.imgur.com/v1lvIk6.png)  

設定完成後，再推一次`git push heroku master`，網站就能順利產生出action cable即時動態效果啦！
![](https://i.imgur.com/G5comrd.gif)


# 完賽感言！

最近利用搭車的空檔看了一本書：超速學習（Ultralearning），
裡面一段話對我來說非常符合身為鐵人賽參賽者的心聲：

> 只要你願意，哪怕你再忙，也能高速學習；只要你懂得再一段時間內，設下高強度的學習挑戰計畫，你將會在短時間裡習得一項高價值的技能！

這次的目標就是為了在鐵人賽30天內，Vue新手如我，能產出一個上線的小作品可以展示（如何在Rails專案裡運用Vue.js）。
為了達成公開設定的目標，每天都黏在電腦桌前研究Vue至少5小時，畢竟已經在網路上面發下豪語了...
這就是參與鐵人賽時，對自己承諾的威力啊！

過去2019鐵人賽也有參賽經驗(當年也是IT鐵人賽+準備馬拉松跑步都擠在10月)，
但這次的挑戰度更高～準備的時間也更少，
這次規劃是前15天研究基本語法、後面15天做專案，算是新的嘗試（測試自己能否在短時間內掌握一門技術並且做出作品的能力），
每天凌晨12點準時發文，存貨不多大概維持1-3篇。

在鐵人賽第15天時曾寫下理想的預計開發項目，但現實中...因為日常還有其他專案要忙碌，即使竭盡所有擠出來的時間和精力，實際完成的功能只有50%而已。
（甚至身旁那位完全不懂Vue的後端工程師也被我抓來問，在此鳴謝～～）

於是我體會到，30天要學好一門技術其實時間太短暫了，
但我磨練到兩個重要的技能ＸＤ =>
1. 怎麼更好地預估自己開發功能的所需時間；
2. 遇到bug卡關時怎麼抽換隔天鐵人賽內容、同時又能確保每天都有達成自己心目中合格的產出，
畢竟參賽是為了完成自己的心願，不是為了別人。

經過30天後，至少對於Vuex比較有概念了，開心！
希望能進一步研究Vue Router，運用在其他專案，
鐵人賽後也要莫忘初衷、持續研究前端框架！


有興趣的看倌請使用這組測試帳號/密碼：
```
visitor@leankanban.com
visitor@lean
```

可以點進去[這裡](https://leankanban.herokuapp.com/)玩玩唷！（請大家鞭小力一點）

如果網站有bug也可以點進去`Kanban`裡在bug區留言XD，我會盡力修改的～

（視網站吞吐量情況，預計2020年底下線～）

最後謝謝五倍紅寶石的龍哥、助教、課務們，第五屆Astro Camp的戰友和PMaster的團隊成員

2020鐵人賽，成功達陣！

![](https://i.imgur.com/TGCczS6.png)

Ref: 

* [網站部署（使用 Heroku）](https://railsbook.tw/chapters/32-deployment-with-heroku.html)  

* [將Ruby on Rails專案部署到Heroku](https://ithelp.ithome.com.tw/articles/10199014)

* [2020鐵人賽專案: Lean Kanban](https://leankanban.herokuapp.com/)  

