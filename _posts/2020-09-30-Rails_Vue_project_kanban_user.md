---
title:  "第12屆鐵人賽Day 17 Rails專案開發 - 第1個Model : 註冊, 登入 UI修改"
preview: ""
permalink: "/articles/2020-09-30"
date:   2020-09-29 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

今天是中秋連假第二天～祝大家月餅柚子烤肉吃爽爽！

話說使用者的註冊登入系統是一個完整的網站不可或缺的功能，

<!-- more -->

[昨天]()規劃好`Kanban`Rails專案進度，把首頁做出來、加上`devise gem`的`navbar`掛上去。

今天來點輕鬆的主題～

在開始做功能前，今天身為一個自己專案的使用者，就來點點看`註冊`、`登入`頁面並且修正一下UI。

# 畫面UI調整

* 美化前

~~devise的預設表單有點陽春~~  
![](https://i.imgur.com/Z4T9nSI.png)

我們趕快來用`tailwind css`改一下
至少要讓註冊表單可以置中啊啊！

`app/views/devise/registrations/new.html.erb`

```
<section class="section-center">
  <h2 class="h2-type">註冊</h2>
  
  <%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
    <%= render "devise/shared/error_messages", resource: resource %>
  
    <div class="field">
      <%= f.label :email, class: 'label' %>
      <%= f.email_field :email, autofocus: true, autocomplete: "email", class: 'input' %>
    </div>
  
    <div class="field">
        <%= f.label "密碼", class: 'label' %>
        <% if @minimum_password_length %>
        <em class="font-thin block">( 最少請輸入<%= @minimum_password_length %>個字元 )</em>
        <% end %>
        <%= f.password_field :password, autocomplete: "new-password", class: 'input' %>

    </div>
  
    <div class="field">
      <%= f.label "請再次輸入密碼", class: 'label'  %>
      <%= f.password_field :password_confirmation, autocomplete: "new-password", class: 'input' %>
    </div>
  
    <div class="actions m-2">
      <%= f.submit "註冊", class: 'main-button' %>
    </div>
  <% end %>
  
  <div class="links-section m-2">
    <%= render "devise/shared/links" %>
  </div>
</section>
```

Tailwind css讓我們可以客製化css class的樣式，
例如，`.section-center`這個class 我們希望可以長得像這樣子：

`app/frontend/styles/application.scss`
  
```
.section-center {
  @apply .px-4 .py-2 .text-center;
  .h2-type {
    @apply .text-2xl .font-thin .mb-2;
  }
}
```

* 稍微美化後  

![](https://i.imgur.com/Ba4zEuk.png)


# 修改登入後的navbar，右上顯示為暱稱

註冊完後，我設計首頁長的如下：

![](https://i.imgur.com/Kljwf6a.png)

（flash的notice訊息也已經調整過;) ）


不過我對於`devise`預設欄位只有出email沒有很滿意，應該要show出稱呼才比較合理

所以，加入一個`name`欄位進入devise預設的user table表單

`rails g migration add_name_to_users`

```
~/Doc/p/pmaster_v/vue_rails

Running via Spring preloader in process 50517
      invoke  active_record
      create    db/migrate/20201001051642_add_name_to_users.rb
```

`20200924015914_add_name_to_users.rb`
```
class AddNameToUsers < ActiveRecord::Migration[6.0]
  def change
    add_column :users, :name, :string    
  end
end
```

接著`rails db:migrate` 讓`name`欄位具現化。

# 讓devise可以接收客製的欄位參數

上一步驟，我們剛剛加完了`name`欄位，接著思考註冊流程：

1. 註冊表單裡，讓使用者在註冊時就可以填寫自己的`name`名稱
2. `name`需要通過devise的permit欄位
3. 使用者未來可以透過編輯帳戶，修改自己的`name`名稱

## MVC: 思考Controller

當使用者送出註冊表單的那個moment，rails會送一包表單的`params`給資料庫，
我們必須要允許新增的欄位進得去資料庫才行。

所以在`application_controller.rb`內增加一個method
`app/controllers/application_controller.rb`

讓`註冊`和`帳戶更新`時，` keys: [:name]`資料都需要被允許：

```
class ApplicationController < ActionController::Base
  before_action :configure_permitted_parameters, if: :devise_controller?

  private
  def configure_permitted_parameters
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name])
    devise_parameter_sanitizer.permit(:account_update, keys: [:name])
  end
end
```

## MVC: 思考Model

讓右上角navbar可以顯示使用者名稱
User.rb
```
  def display_name
    # 如果有name就顯示，不然才顯示email
    name || email
  end
```

## MVC: 改寫View

我們讓navbar秀出來使用者的名字，並且把更新`帳戶`的功能做出來，讓使用者更容易編輯自底的帳戶
```
<div>
  <% if user_signed_in? %>
    <span class="mr-2">Hi! <%= current_user.display_name %></span>
    <%= link_to '帳戶 ', edit_user_registration_path, class: 'user-button' %>            
    <%= link_to '登出', destroy_user_session_path, method: 'delete', class: 'user-button' %>
  <% else %>
    <%= link_to '登入 ', new_user_session_path, class: 'user-button' %>
    <%= link_to '註冊 ', new_user_registration_path,  class: 'user-button' %>
  <% end %>
</div>

```

註冊表單
```
  <h2 class="h2-type">註冊</h2>
  
  <%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
    <%= render "devise/shared/error_messages", resource: resource %>
  
    <div class="field">
      <%= f.label "名稱", class: 'label' %>
      <%= f.text_field :name, autofocus: true, autocomplete: "name", placeholder: "我們該如何稱呼您？", class: "input" %>
    </div>
    <!-- 略 -->
  <%= end %>  
```

編輯帳戶表單，增加可以修改`name`欄位
```
  <h2 class="h2-type">編輯<%=current_user.email%>帳戶</h2>
  
  <%= form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
  <%= render "devise/shared/error_messages", resource: resource %>

    <div class="field">
      <%= f.label "名稱", class: 'label' %>
      <%= f.text_field :name, autofocus: true, autocomplete: "name", placeholder: "變更名稱", class: "input" %>
    </div>
    <!-- 略 -->    
```

這樣使用者就能透過網頁`UI`修改自己的帳戶名稱囉！  

![](https://i.imgur.com/ie0QMtM.png)

今天本來要一口氣做到使用者信箱驗證還有邀請另一個使用者進入`Kanban`...
不過細切下來其實整個步驟有點繁瑣，難得連假還是放鬆一下心情好了～

（果然計劃趕不上變化啊！）
明天待續！

Ref: 

* [GitHub: devise gem](https://github.com/heartcombo/devise)  

* [tailwindcss](https://tailwindcss.com/)  




