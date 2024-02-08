---
title:  "第12屆鐵人賽Day 29 Rails專案開發 - Action Cable即時互動功能: 以edit和delete ticket為例"
preview: ""
permalink: "/articles/2020-10-12"
date:   2020-10-10 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

來到倒數第二天啦！感動流淚O_Q

前兩天裡用Vuex狀態管理的方式實作`編輯`和`刪除`ticket

本日待實作的功能：利用Action Cable廣播，在任何的瀏覽器登入，都會同步顯示編輯和刪除的結果～讓使用者有即時互動的體驗！

如下圖所示，我開了兩個瀏覽器(Safari和chrome)，無論在哪個瀏覽器`編輯`或`刪除`ticket，另一個瀏覽器都會即時產生效果～

![](https://i.imgur.com/8b7UQPc.gif)

# WebSocket: 讓前端與後端沒有距離

WebSocket是能讓瀏覽器(Client端)與伺服器(Server端)持續進行`雙向資料傳遞`的通訊協定，Client端用Ajax非同步方式做請求， Server端也能主動發送Client所需要的資料。

相信大家都有聽過HTTP吧～HTTP是基於TCP(Transmission Control Protocol, 傳輸控制協定)進行「三向交握（3-way handshake）建立連線（A方提出請求、B方確認A的請求並提出自己請求、A方再確認一次B的請求，總共三次。）

而WebSocket是進階的網路應用層協議（在2011年由[IETF](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E5%B7%A5%E7%A8%8B%E4%BB%BB%E5%8A%A1%E7%BB%84)標準化）。如果Client端初始化時HTTP標頭中含有WebSocket的資訊，當連線Client端到Server端，Server端會將HTTP連線升級為WebSocket的連接，並返回同樣包含websocket標頭資訊的HTTP回應，就能`立即實現持續的雙向溝通`，因此WebSocket常用來實作可以互相傳訊息的聊天室。

# ActionCable: 整合client端(JavaScript介面)以及server端

ActionCable是建構於WebSocket上、Pub（消息發佈者）/Sub（消息訂閱者）模型的Ruby on Rails框架，Publisher和Subscriber會以javascript透過非同步(async)的方式傳遞訊息，使訊息傳遞不需等待回應就可進行後續操作（想像聊天的時候，你不用等收到別人訊息的時候才能傳出自己的訊息），非常便利！接下來今天的文章開始參考[Rails Guides: Action Cable](https://guides.rubyonrails.org/action_cable_overview.html) ，並使用`Redis`這款帶有`Pub/Sub`功能的資料庫實作。

# Step1. 設定Server端連線

通常看到網路上的教學步驟，都會請你先new一個rails專案、建立User model以及必要的controller。
由於我們現在這個鐵人賽專案已經有了很多個controller（kanban, column, ticket...XD），就直接挑比較符合業務邏輯的controller加上channel。

首先我來`rails g channel`建立一個channel，名叫`column` 

```
rails g channel column

Running via Spring preloader in process 70377
      invoke  rspec
      create    spec/channels/column_channel_spec.rb
      create  app/channels/column_channel.rb
```

## 1-1 訂閱頻道

接著來修改`channels/column_channel.rb`這個檔案，在裡面指定訂閱的頻道是剛剛建立的`column` 
```
class ColumnChannel < ApplicationCable::Channel
  def subscribed
    stream_from "column"
  end

  def unsubscribed
  end
end
```

## 1-2 建立連線前，進行身份驗證

想像一個軟體有群組聊天以及私人一對一聊天的功能，我們需要符合身份的人（也就是有`訂閱`這個頻道的人），有值的話才能回傳給他特定的訊息，不然就拒絕連線。
那要怎麼實作這個功能呢？

最一開始使用Rails的`devise gem`製作註冊登入系統，裡已經提供給我們適當的環境變數`env["warden"]`。
在這裡，把它透過`byebug`印出來看看:

```
10:49:52 web.1       | (byebug) env["warden"]
10:49:52 web.1       | Warden::Proxy:70111027317460 @config={:default_scope=>:user, :scope_defaults=>{}, :default_strategies=>{:user=>[:rememberable, :database_authenticatable]}, :intercept_401=>false, :failure_app=>#<Devise::Delegator:0x00007f87fa2cc638>}

10:49:52 web.1       | (byebug) env["warden"].user
10:50:30 web.1       | #<User id: 1, email: "tingtinghsu[at]秘密", created_at: "2020-10-01 01:43:03", updated_at: "2020-10-01 07:12:27", name: "Ting">
```

我們在`channels/application_cable/connection.rb`進行連線前的身份驗證，如果`env["warden"].user`有值，指定給`current_user`

```
module ApplicationCable
  class Connection < ActionCable::Connection::Base
    identified_by :current_user

    #這裡引入current_user
    def connect
      self.current_user = find_verified_user
    end

    private
      def find_verified_user
        if current_user = env["warden"].user
          current_user
        else
          reject_unauthorized_connection
        end
      end
  end
  end
end

```

# Step 2. 設定client端連線

接下來是瀏覽器端了！

## 2-1 Consumer.js (Rails的產生預設檔案)

雖然這一份js檔案不是我們自己寫的，不過還是可以來研究一下大神們開發的架構，這個檔案會讓consumer預設與server的cable做連線。

```
// Action Cable provides the framework to deal with WebSockets in Rails.
// You can generate new channels where WebSocket features live using the `rails generate channel` command.

import { createConsumer } from "@rails/actioncable"
export default createConsumer()
```

## 2-2 Consumer連線後，會成為Subscriber`訂閱者`：建立 column_channel.js

Consumer連線後成為Subscriber後就會進來這裡。有三個部分：`connected()`、`received(data)`、`disconnected()`
我們可以在瀏覽器的dev tool裡console分別印出連線狀況，幫助debug。

`app/frontend/channel/column.js`
```
import consumer from "./consumer"
console.log("loading")

consumer.subscriptions.create("ColumnChannel", {
  connected() {
    console.log("connected")
  },

  received(data) {
    console.log("Welcome to ColumnChannel")    
    console.log(data)

    if(data.commit){
      //等等會用到這個區塊 
    }
  },

  disconnected() {
    console.log("disconnected")
  }
});
```

![](https://i.imgur.com/BqI4Oys.gif)

# Step 3. Rails MVC: 透過 controller 廣播

## 3-1 統一讓後端資料變更成功時廣播送出commit

把專案的編輯刪除ticket的controller動作結合action cable，資料更動成功的話，透過`ActionCable.server.broadcast`對`column channel`做廣播，要commit的那一包payload為轉為string的`:show.json`。

```
  def update
    respond_to do |format|
      if @ticket.update(ticket_params)     
      ActionCable.server.broadcast("column", { commit: 'UPDATE_TICKET', payload: render_to_string(:show, format: :json)})        
        format.json { render :show, status: :ok}
      else
        format.json { render json: @ticket.errors, status: :unprocessable_entity }
      end
    end
  end

  def destroy
    @ticket.destroy
    respond_to do |format|
      puts "destroy success"
      ActionCable.server.broadcast("column", { commit: 'DELETE_TICKET', payload: render_to_string(:show, format: :json)})       
      format.json { head :no_content }      
    end
  end
```
ps.這邊的點我卡了幾個小時，因為一直沒有render出自己要的json檔，後來發現要把scaffold長出來沒用到的html.erb先刪掉，Rails才不會找錯檔案。

## 3-2 修改Vuex action，前端打資料去後端時不需重複commit

記得把原本Vuex action內的commit註解掉，因為現在我們已經透過controller進行全域的廣播了（不然會操作的那一方瀏覽器會commit兩次）
```
  actions: {
    updateTicket({ commit }, {id, name}){
      // 略
      Rails.ajax({
        url: `/kanbans/${el.dataset.kanbanid}/tickets/${id}`,
        type: 'PUT',
        data,
        dataType: 'json',
        success: result => {
          // commit("UPDATE_TICKET", result);
          console.log(result);
        },
        error: error => {
          console.log(error);            
        }
      });      
    },
    deleteTicket({ commit }, {ticket_id, column_id}){
      //略
      Rails.ajax({
        url: `/kanbans/${el.dataset.kanbanid}/tickets/${ticket_id}`,
        type: 'DELETE',
        dataType: 'json',
        success: result => {
          // commit("DELETE_TICKET", {ticket_id, column_id});
        },
        error: error => {
          console.log(error)
        }
      });
    },
```


# Step 4. `Subscriber`訂閱者處理需要commit的data

## 4-1. 讓Vuex的`$store`變成全域變數

之前做專案時，如果是透過webpacker引入jQuery套件，有一個~~偷吃步的~~方法可以把`$`字符號設為全域都可使用
就是在`application.js`加上`window.$ = $`
```
import $ from 'jquery'
window.$ = $
```

那麼為了讓我們可以在`Action Cable`的client端也可以對store做事情，
我們如法炮製，在`application.js`這個Vue的掛載點加上這句：

```
window.$store = store;
```


## 4-2. `Subscriber`訂閱者收到資料`received(data)`

vuex限制`commit`只能傳2個參數，第一個是function名稱，第二個用object包起來的參數（稱為payload）。
我們把收到的data，利用`window.$store.commit(data.commit, JSON.parse(data.payload)); `重新渲染自己的頁面

`app/frontend/channel/column.js`
```
import consumer from "./consumer"

consumer.subscriptions.create("ColumnChannel", {
  connected() {
  },

  received(data) {
    if(data.commit){
      console.log("data commit!")
      window.$store.commit(data.commit, JSON.parse(data.payload));      
    }
  },

  disconnected() {
  }
});
```


完成摟！在哪個瀏覽器`編輯`或`刪除`ticket，另一個瀏覽器都會即時產生效果～

![](https://i.imgur.com/8b7UQPc.gif)

實作完今天的單元後，又對於WebSocket這個瀏覽器(Client端)與伺服器(Server端)進行`雙向資料傳遞`的通訊協定更加清晰了！


心得：

1. 這並不是我第一次實做action cable，但是第一次透過結合Vuex狀態管理來做`ActionCable.server.broadcast`，算是一個自己的大突破～～本來預計想做的是拖拉的action cable即時效果（但還沒克服bug）緊急換成實作`刪除ticket`和`修改ticket`的即時互動效果～終於在自己規定的時限內研究出來+把文章寫好。
2. 後來發現Vue自己也有[actioncable-vue](https://github.com/mclintprojects/actioncable-vue) 的套件，鐵人賽結束後我也會來玩玩看！
3. 做完action cable功能終於能夠呼應本次鐵人賽的名稱「前端後端交響曲」了～～放煙火！

  
Ref: 

* [Rails Guides: Action Cable](https://guides.rubyonrails.org/action_cable_overview.html)  

* [Rails Actioncable 即時通訊](https://medium.com/@dd0425/rails-actioncable-%E5%8D%B3%E6%99%82%E9%80%9A%E8%A8%8A-eea02474ff33)  

* [WebSocket 讓前後端沒有距離](https://medium.com/enjoy-life-enjoy-coding/javascript-websocket-%E8%AE%93%E5%89%8D%E5%BE%8C%E7%AB%AF%E6%B2%92%E6%9C%89%E8%B7%9D%E9%9B%A2-34536c333e1b)  

* [TCP 三向交握 (Three-way Handshake)](https://notfalse.net/7/three-way-handshake)  

* [actioncable-vue](https://github.com/mclintprojects/actioncable-vue)  