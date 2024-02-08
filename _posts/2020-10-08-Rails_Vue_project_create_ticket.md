---
title:  "第12屆鐵人賽Day 25 Rails專案開發 - 新增ticket"
preview: ""
permalink: "/articles/2020-10-08"
date:   2020-10-04 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---


盤點這一週的完成項目：

我們現在學會了：
- 把Rails專案裡會重複使用的部分改寫成Vue 元件（`column`, `ticket`）
- 使用Vue套件`Vue draggable`完成拖拉效果
- 利用`Vuex`來管理狀態

而且在看板裡開了兩張ticket都已經了拖到`DONE`的欄位了！；）

距離鐵人賽完賽還有5天，還可以做2~3個新功能，

例如：接下來還想要增加新的`ticket`，像是加入Rails ActionCable即時互動的功能這張票。

Wait a moment！我發現網站介面還沒有可以新增ticket的地方啊啊啊  
（還記得之前兩張ticket是一開始透過Rails Console 加進資料庫裡的嗎~）

那麼，今天就來製作`新增ticket`的UI及功能吧！  

# 元件：三大區塊

在開始更動component之前，我們先複習一下每個元件都會有的三個區塊：
分別放`html`的`template`、動態的`javascript`和`style`的樣式

```javascript
<template>
// 這裡放html
</template>

<script>
// 引入其他元件
// import Another from "/components/another.vue";

export default {
  name: '',
  data: function () {
    return {
    };
  },
  // 註冊其他元件
  // components: { Another },
};
</script>

<style scoped>
// 這裡放css
</style>
```

結構複習完之後，就可以知道要在哪個區塊更動程式碼了！

# 新增 ticket
## `template`: 新增輸入框

新增ticket的功能會放在`column component`，因為column是利用`v-for`跑迴圈、列出所有ticket的地方，所以我們會使用`input`和`button`來製作可以新增ticket的UI：

```
<template>
  <div class="column">
    <div class="column-name">{{ column.name }}</div>
    <div class="ticket-list">
      <draggable v-model="tickets" group="column" @change="dragTicket">
        <Ticket v-for="ticket in tickets" :ticket="ticket" :key="ticket.id"></Ticket>
      </draggable>
      <div class="ticket-input">
        <input class="ticket-name"></input>        
        <button class="create-ticket-btn"><i class="fas fa-plus text-teal-100"></i></button>
      </div>
    </div>
  </div>
</template>
```

![](https://i.imgur.com/9n0fgoA.png)

疑~怎麼長得有點醜!?趕快用`Tailwind CSS`來調一下樣式，我想來點`3M便利貼`的感覺～

## `style`: 設計輸入框

例如：
```
<style lang="scss" scoped>
  .column{
    @apply .bg-teal-100 .mx-2 .my-2 .w-64 .px-3 .py-2 .rounded-md;
    .column-name{
      @apply .font-thin .m-2  .border-dashed .border-gray-600;
    }

    .ticket-list{
      .ticket-input {
          @apply .mt-2;
        .ticket-name {
                  @apply .w-full .px-2 .py-1 .rounded-md .bg-yellow-100;
        &:focus {
          @apply .outline-none;
        }
        }
        .create-ticket-btn {
          @apply .mx-1 .my-2 .px-2 .py-1 .bg-teal-200 .rounded-md .text-sm .text-gray-800 .font-thin;
  &:hover {
    @apply .bg-teal-300;
  }
        }
      }
    }
  }
</style>
```

嗯嗯，感覺順眼多了！  
![](https://i.imgur.com/QUn42OX.png)


我們在專案裡修改css的經驗，是團隊其他人所寫出來的結構常常不太一樣，讓接手的人也很難知道哪個檔案位置去更動他人寫的樣式。而Vue元件的好處就是能夠把跟該元件的css檔案，統一集中管理在一起，不用到其他的css檔案去翻找，非常便利！

## `template`: 按下`+`按鈕時，把資料送到後端去

還記得鐵人賽第5天的雙向事件處理: `v-model`, `v-on`嗎? 我自己做功能的時候，常常會回去看之前自己寫的鐵人賽文章複習重要觀念。

我想要讓使用者`輸入ticket的名稱`時，使用`v-model`讓ticket可以即時顯示。  
並且讓使用者按下`button`時，呼叫`thankyou`這個methods裡面的方法。  

因此，我們在`template`裡`綁定`@click`的`createTicket`method:

```
<template>
  <div class="column">
    <div class="column-name">{{ column.name }}</div>
    <div class="ticket-list">
      <draggable v-model="tickets" ghost-class="ghost" group="column" @change="dragTicket">
        <Ticket v-for="ticket in tickets" :ticket="ticket" :key="ticket.id"></Ticket>
      </draggable>
      <div class="ticket-input">
        <input class="ticket-name" v-model="ticketname"></input>        
        <button class="create-ticket-btn" @click="createTicket"><i class="fas fa-plus text-teal-100"></i></button>
      </div>
    </div>
  </div>
</template>
```

## `script`: 把ticket的name從console顯示出來

接下來就是撰寫`createTicket()`方法啦，我們使用`preventDefault`卡住，把接到的`event`印出來看看：

```
<script>
    methods: {
      createTicket(evt){
        evt.preventDefault();
        console.log(this.ticketname)
      },
      dragTicket(evt){
        //略
      }
    }
  }
</script>
```

如下Gif圖所示，console裡可以抓到正確的ticket名稱：

![](https://i.imgur.com/tqEohmh.gif)

## `rails`打資料到後端

之前寫`dragTicket()`method時，已用過`Rails.ajax`，
所以`component`已經引入了套件：
`import Rails from '@rails/ujs';`

第21天的時候也有說過，FormData是[Web API: Form Data](https://developer.mozilla.org/zh-TW/docs/Web/API/FormData)提供表單格式，我們可以利用它將資料轉成表單的格式，並且以表單的形式回傳給後端。

我們利用FormData new出一包data，append新的`ticket`打給後端，利用`POST`方法存進database:

```
    methods: {
      createTicket(evt){
        event.preventDefault();
        console.log(this.ticketname)
        let data = new FormData();
        data.append("ticket[column_id]", this.column.id);
        data.append("ticket[name]", this.ticketname);
        Rails.ajax({
            url: `/kanbans/${this.column.kanban_id}/tickets`,
            type: 'POST',
            data,
            dataType: 'json',
            success: response => {
              console.log(response)
              this.tickets.push(response);
              // 清空輸入框
              this.ticketname = "";              
            },
            error: err => {
              console.log(err)
            }
          });
      },
      dragTicket(evt){
        //略
      }
    }
```


按下`新增卡片`，console會把收到的一包資料印出來。
但是目前仍然需要要重新整理網頁才會生效。如何讓`ticket`立即更新到前端呢？


## 修改controller: `tickets#create`

最後，需要讓json render出我們所需的資料欄位。
目前我們的jbuilder選出的資料格式如下（只保留需要的欄位）：

_ticket.json.jbuilder
```
json.extract! ticket, :id, :name, :column_id, :position
```
如果`create`成功，render出一包新的ticket json:

tickets_controller.rb：
```
  def create
    @ticket = Ticket.new(ticket_params)
    respond_to do |format|
      if @ticket.save
        format.json { render :show, status: :created }
      else
        format.json { render json: @ticket.errors, status: :unprocessable_entity }
      end
    end
  end
```

終於順利地新增更多`ticket`、把開發更多功能的票，記錄在自己的專案囉！

![](https://i.imgur.com/n40WO2j.gif)  
