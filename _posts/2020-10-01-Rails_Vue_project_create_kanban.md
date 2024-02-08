---
title:  "第12屆鐵人賽Day 18 Rails專案開發 - 第2個Model: 建立看板 Kanban"
preview: ""
permalink: "/articles/2020-10-01"
date:   2020-09-30 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

話說前幾天聽了[星箭廣播「如何變強」特輯：工程師學習新技術](https://blog.starrocket.io/posts/star-rocket-podcast-ep44-lawrence-lin-talks-about-learning-a-new-technology-react/)，深受啟發。這集的來賓是創業家林宜儒（Lawrence）同時也是一位資深的Rails後端工程師，他分享了自己學習前端框架的心得（以`React`為例）。我覺得很值得跟鐵人賽的朋友們分享！

<!-- more -->

在這集的概念Lawerence也提到：
- 以終為始（begin with the end in mind）：先確認好目標，然後再以目標為前提去設計達成步驟。
- 漸進式的學習：他自己是先用Rails把功能做出來，再逐步換成前端的Component。

而我的目標就是要讓Rails專案有`最小可行`的產品，其中有幾塊功能是用`Vue`的元件實現，而且在鐵人賽第30天以前把網站部署上去；）

前言講完～來開發網站吧！

# 建立看板Kanban

還記得昨天的最後專案完成度：放了一顆`新增Kanban`的按鈕，但是現在這個按鈕裡點下去暫時沒有功能。  
  
![](https://i.imgur.com/ie0QMtM.png)  

而Rails的強項就是打造快速CRUD（`新增`, `刪除`, `修改`, `查詢`）功能，為了加快產品開發速度，我先用`Scaffold`產出鷹架，把Kanban的新增、修改、刪除功能一口氣都做出來。專案接近尾聲時再整理一下多餘的程式碼。


```
rails g scaffold Kanban name description user:belongs_to
```

接著修改Model關聯、再`rails db:migrate`讓我們第二張table `Kanban`具現化。


# 建立看板

為了要讓我們點擊按鈕後，可以出現類似如下圖的畫面，我們需要進行下列步驟：

![](https://i.imgur.com/9CuPVZw.png)

## MVC: model - User可以有很多`Kanban`

user.rb
```
class User < ApplicationRecord
  has_many :kanbans

  def display_name
    name || email
  end  
end
```

kanban.rb
```
class Kanban < ApplicationRecord
  belongs_to :user
end
```

## MVC: controller - 讓新建的`kanban`帶user_id
```
  def new
    @kanban = current_user.kanbans.new
  end

  def create
    @kanban = current_user.kanbans.new(kanban_params)
    //略
  end
```

## MVC: view - 設計UI

```
<%= form_with(model: kanban, local: true) do |form| %>
  <% if kanban.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(kanban.errors.count, "error") %> prohibited this kanban from being saved:</h2>

      <ul>
        <% kanban.errors.full_messages.each do |message| %>
          <li><%= message %></li>
        <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <div>
      <%= form.label "看板名稱", class: "text-teal-700" %>
    </div>
    <%= form.text_field :name, class: "input is-success m-3 bg-teal-200 rounded-md px-3 py-2" %>
  </div>

  <div class="field">
    <div>
      <%= form.label "看板描述", class: "text-teal-700" %>
    </div>
    <%= form.text_area :description, class: "input is-success m-3 bg-teal-200 rounded-md px-3 py-2" %>
  </div>

  <div class="actions m-3">
    <%= form.submit "建立看板", class: "bg-teal-300 px-3 py-2 rounded" %>
  </div>
    <%= link_to '回上一頁', dashboard_path, class: "bg-yellow-300 px-3 py-2 rounded" %>
<% end %>

```

# 設計User登入後的主頁`dashboard`

建立好看板之後，我希望使用者能夠在登入後時，能在`dashboard`看到所有自己的看板

## 設計route

`pages#dashboard` 會是我放所有使用者看板的地方

routes.rb
```
Rails.application.routes.draw do
  resources :kanbans //未來會簡化此條路徑
  devise_for :users
  root 'pages#index'
  get "dashboard", to: "pages#dashboard"
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end
```

## MVC: controller

pages.controller.rb

當使用者一旦成功登入系統，index會把使用者導向`dashboard`，而我們希望在`dashboard`這頁能看到使用者所有看板，因此會需要`@kanbans`這個實體變數。

```
class PagesController < ApplicationController
  before_action :authenticate_user!, only: [:dashboard]  

  def index
    if current_user
      redirect_to dashboard_path
    end    
  end

  def dashboard
    @kanbans = current_user.kanbans.all    
  end
end
```

## MVC: view - 設計UI

`app/views/pages/dashboard.html.erb`

用tailwind把kanban的排列方式設計成卡片陳列的樣子

```
<section class="section hero text-center">
  <h1 class="title text-xl mt-5 text-teal-500">歡迎登入 <%= current_user.display_name%>的精實看板！</h1>
  <br>
  <%# <h2 class="subtitle"></h2> %>
    <div class="text-md flex">
      <div class="flex-1">
        <% @kanbans.each do |kanban| %>
        <div class="max-w-sm rounded overflow-hidden shadow-lg m-5">
          <div class="px-6 py-4">
  
            <div class="font-bold text-xl mb-2 fas fa-user-plus"></div>
            <div class="font-bold text-xl mb-2"><%= link_to kanban.name, kanban_path(kanban) %></div>
            <p class="text-gray-700 text-base">
              <%=kanban.description %>
            </p>
          </div>
          <!-- 略 -->
        </div>
      </div>
      <% end %>

      <div class="font-light mt-5 center"><%= link_to '新增Kanban', new_kanban_path, class: "font-light button py-2 px-3 bg-teal-200 rounded" %></div>
      </div> 
    </div>
</section>

```

完成了看板首頁的設計～
連假前兩天都還在`Rails`基礎範疇裡，接下來要增加更多的Model，並且開始掛Vue的component了！
（邀請其他使用者進入Kanban，目前已經在畫面上顯示用`fontawesome`加入人像的icon，實際功能會移到下週的時候再進行開發～）  

![](https://i.imgur.com/z2srPz1.png)



明天待續！