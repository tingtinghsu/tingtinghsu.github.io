---
title:  "第12屆鐵人賽Day 20 Rails專案開發 - 建立Vue元件: Ticket component"
preview: ""
permalink: "/articles/2020-10-03"
date:   2020-09-30 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

[昨天的鐵人賽](https://ithelp.ithome.com.tw/articles/10248216)我們終於開始真正把手上在開發的系統變成，Vue.js x Rails專案了，可喜可賀！

今天，我想開給自己兩張票`ticket`，包括明後兩天要製作的`ticket拖拉功能`以及`改寫vuex狀態管理`

想像中的畫面大概會長醬子：

![](https://i.imgur.com/syN8XfL.png)

(一種開票給自己做的概念XD)

該如何設計`Ticket`元件呢?

# 首先：建立第4個Model: Ticket

- 使用者註冊登入系統
- 使用者可以建立很多`Kanban`
- 看板`Kanban`可以有很多`Column`
- 每個`Column`可以有很多張票`Ticket`

kanban.rb

每個kanban有很多columns, 根據`dependent`，如果刪掉了kanban，column也就一起刪掉

```
class Kanban < ApplicationRecord
  has_many :columns, dependent: :destroy
  belongs_to :user
  validates :name, presence: true
end
```

column.rb

每個column有很多ticket, 根據`dependent`，如果刪掉了column，ticket也就一起刪掉
```
class Column < ApplicationRecord
  has_many :tickets, dependent: :destroy
  belongs_to :kanban
  validates :name, presence: true  
end
```

ticket.rb
```
class Ticket < ApplicationRecord
  belongs_to :column
  validates :name, presence: true  
end
```


# 在rails console先建立幾筆ticket資料

```
c1.tickets.create(name: "ticket拖拉功能")
c1.tickets.create(name: "改寫vuex狀態管理data")
```

# 設計ticket元件

## Step 1. 確定ticket元件的資料要從哪裡得到

jbuilder會幫我們把資料輸出為json格式。如果我們想把`column`下面的所有tickets也一起抓回來，可以`_column.json.jbuilder`這份檔案裡，後面再加上`:tickets`

```
json.extract! column, :id, :name, :kanban_id, :tickets
```

如此一來，`/kanbans/2/columns.json`的路徑下的json檔資料變成如下：

```
[
  {
    "id": 1,
    "name": "Backlog",
    "kanban_id": 2,
    "tickets": [
      {
        "id": 1,
        "name": "ticket拖拉功能",
        "column_id": 1,
        "position": 1
      },
      {
        "id": 2,
        "name": "改寫成vuex 管理狀態",
        "column_id": 1,
        "position": 2
      }
    ]
  },
  {
    "id": 2,
    "name": "Sprint Backlog",
    "kanban_id": 2,
    "tickets": [
      
    ]
  },
  //略
]
```

## Step 2. `Vue app`的掛載點，改寫：

我們要在column元件裡面再包含ticket元件，因此掛載點的template會需要使用到`<Ticket></Ticket>`，並透過`v-for`把column裡面的tickets用迴圈轉出來

而`:ticket="ticket`的意思是
是v-for迴圈的每個`ticket`值，透過`:ticket`傳給子元件

最後，別忘了
引入`import Ticket from "components/kanban/ticket"`
並註冊子元件`components: { Ticket }`

```
<template>
  <div class="column">
    <div class="column-name">{{ column.name }}</div>
    <div class="ticket-list">
      <Ticket v-for="ticket in column.tickets" :ticket="ticket" :key="ticket.id"></Ticket>
    </div>
  </div>
</template>

<script>
  // 讓用元件的父層，餵資料給子元件
  import Ticket from "components/kanban/ticket"  
  export default {              
    name: 'Column',
    props: ["column"],
    components: { Ticket }
  }
</script>

<style lang="scss" scoped>
<!-- 略 -->
</style>
```

## Step 3. 設計ticket元件

與父元件相比，子元件相對單純的多，透過`props`拿到父元件傳進來的值，即可透過鬍子語法把票印出來。
```
<template>
  <div class="ticket">
    {{ ticket.name }}
  </div>
</template>

<script>
  export default {              
    name: 'Ticket',
    props: ["ticket"]
  }
</script>

<style lang="scss" scoped>
  .ticket {
    @apply .bg-yellow-100 .mb-2 .rounded-md .px-2 .py-2 .font-thin .text-sm .shadow-lg;
  }
</style>
```


如`Vue`的`devtool`所示，`ticket component`順利掛在`column component`下囉！
![](https://i.imgur.com/rd6xTna.png)

明天來做拖拉功能！

最後，以一句我很喜歡的話送給大家～共勉之：）  
> Mapping our work allows us to navigate our life.  
> 將工作視覺化在看板上，使我們能夠駕馭生活  - Jim Banson

