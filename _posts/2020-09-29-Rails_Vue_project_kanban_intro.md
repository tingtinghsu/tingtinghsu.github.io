---
title:  "第12屆鐵人賽Day 16 Rails專案開發 - 網站進度規劃"
preview: ""
permalink: "/articles/2020-09-29"
date:   2020-09-23 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

前情提要：我們在第3天的時候為Rails專案裡導入了Vue.js框架，

接著花了10天左右的時間熟悉Vue重要的基本語法～

<!-- more -->

# 嘗試2週內走一輪敏捷開發

在接下來的兩週，我想開發一個系統，做出適合自己用的個人看板、或是未來2到5人團隊跑`scrum`流程的使用的看板`Kanban`來管理專案進度。

並且加上Rails Action Cable的即時互動效果，讓團隊能將看板上顯現出來的問題，一起討論分析、尋求每一次專案跑`scrum`流程後，都能調整與改進。

我在`trello`裡面找到一個適合致敬的`Software Development` template。

![](https://i.imgur.com/TP0g3qY.png)

並且用這個鐵人賽開發的`kanban`來管理自己鐵人賽的專案 ;)


# 想開發的功能，及預期開發進度

- [day16] 網站功能規劃、Route設計、前端套件安裝；使用者的註冊登入系統 (使用 Rails `devise` gem)
- [day17] 使用者可以新建`Kanban`，並email邀請其他成員加入 (使用`Mailgun` API)
- [day18] 看板`Kanban`可以讓使用者建立很多`Column`，例如`Sprint backlog`, `Doing`, `Testing`, `Done`等欄位。每個`Column`可以有很多張票`Ticket`。
- [day19] 把`Kanban`, `Column`, `Ticket`改成`Vue Component`。
- [day20] 使用`Vue draggable`，讓`Column`和`Ticket`可以拖拉。
- [day21-24] 以`Vuex`狀態管理新增/修改/刪除`Column`和`Ticket`。
- [day25-28] 如果A使用者對`Column`和`Item`的新增/修改/刪除，會在B使用者的畫面同步顯示。  (使用 Rails `Action Cable`)  
-  [day29] 專案上線(使用`Heroku`進行deploy)  
-  [day30] 鐵人賽總結～～


# 在webpacker引入前端套件

`fontawesome` : 我們會在首頁的`navbar`以及功能按鈕用到小圖示
`Tailwind Css`: 調整`Column`和`ticket`等前端顯示的樣式

使用`yarn`安裝
```
yarn add @fortawesome/fontawesome-free
yarn add tailwindcss
```

在rails裡的`applictaion.js`檔，require某個資料夾時，都會自動抓的index.js檔。  

所以從`node_modules`找我們要引入的檔案，放在`fontend/styles/index.js`
用來引入專案需要用到的的css或js檔案。

```
import "@fortawesome/fontawesome-free/css/all.css";

import "tailwindcss/base";
import "tailwindcss/components";
import "tailwindcss/utilities";
```

# 後端套件: `devise` 註冊登入系統

Devise為Rails最常使用的會員系統套件，為了讓我們專案能夠快速上線，就透過`Devise`完成網站基本的註冊登入功能。

安裝`gem`之後，記得`bundle install`，然後重啟server
```
gem 'devise', '~> 4.7', '>= 4.7.1'
```

接下來的步驟比較無趣～就不細說了XD
```
/vue_rails  master !7 ?1 ▓▒░ rails g devise:install

# 產生user model
/vue_rails  master !8 ?4 ▓▒░ rails g devise User 

# 為了能夠客製化註冊登入頁面，把devise的產生view
/vue_rails  master !9 ?8 ▓▒░ rails g devise:views      
```


# 規劃首頁routes

專案開發第一天，先來個首頁路徑，以及設計首頁

```
Rails.application.routes.draw do
  devise_for :users  
  root 'pages#home'

end
```

如果在`rails console`輸入：`rails routes | grep devise`

就會發現，routes上的一行程式碼`devise_for :users`會幫我們長了好多路徑

![](https://i.imgur.com/sunFXEZ.png)

# 製作首頁的`navbar` 

我們想為首頁製作`navbar`，並且把devise路徑放進`註冊`、`登入`、`登出`按鈕
`_navbar.html.erb`
```
    <nav class="bg-teal-700 text-blue-100">
      <div class="font-thin flex justify-between item-center">
        <a href="/">
          <i class="fas fa-columns">
          </i>
          Lean Kanban 精實看板
        </a>
        <div>
          <% if user_signed_in? %>
            <span class="mr-2"><%= current_user.display_name %></span>
            <%= link_to '登出', destroy_user_session_path, method: 'delete', class: 'user-button' %>
          <% else %>
            <%= link_to '登入 ', new_user_session_path, class: 'user-button' %>
            <%= link_to '註冊 ', new_user_registration_path,  class: 'user-button' %>
          <% end %>
        </div>
      </div>
    </nav>
```


`_navbar`寫成partial檔，是為了讓 `application.html.erb` render進去，讓`view`的結構更清晰

```
<!DOCTYPE html>
<html>
  <head>
    <title>Vue.js x Rails 鐵人賽專案</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_pack_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>

  </head>
  <body>
    <%= render "shared/navbar" %>
    <%= render "shared/flash" if notice %>  
    <%= yield %>
  </body>

</html>
```

再放上自己挑選的圖和用`tailwind css`設計icon～我們的專案首頁就完成囉！


![](https://i.imgur.com/CMi0Ztr.png)












