---
title:  "第12屆鐵人賽Day 22 Rails專案開發 - Vue draggable套件拖拉ticket"
preview: ""
permalink: "/articles/2020-10-05"
date:   2020-10-03 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

已經來到第22天了！剩下一週的時間我們的專案就要部署（顯示為加緊趕工）～

昨天的鐵人賽把`column`拖拉完成，

今天的需求：拖拉`ticket`，而且可以移動到不同的`column`。

在今天的鐵人賽結束後，我想把放在`backlog`欄位的`ticket`拖到`Done`，代表今天即將完成的這張票！

目前我們的ticket model長這樣：

ticket.rb
```
class Ticket < ApplicationRecord
  // 同一個column的scope下，每個ticket的position值是unique的
  acts_as_list scope: :column  
  belongs_to :column
  validates :name, presence: true  
end
```

# Step 0. 引入`draggable`套件並註冊元件

拖拉`ticket`會比拖拉`column`還要複雜一點，因為`ticket`上面還有一層父元件，且`ticket`可能會被放在其他的`column`裡面。

因此除了註冊元件之外，我們要透過讓元件的父層`column`，餵資料給子元件`ticket`的方式，使用`props: ["column"]`把接下來步驟需要的資料餵進來：

ticket.vue

```javascript
<script>

  import Ticket from "components/kanban/ticket"
  import draggable from 'vuedraggable';

  export default {              
    name: 'Column',
    //  
    props: ["column"],
    components: { Ticket, draggable },    
    // 每個元件都有自己的狀態和資料，所以data需要給一個能return值回去的function 
    data: function () {
      return {
        tickets: this.column.tickets   
      }
    }
  }
</script>

```

# Step 1. 在要拖拉的ticket加入`draggable components`並綁定v-model

如同昨天鐵人賽的文章所示範，只要在`Ticket`元件外層加上`draggable`元件：

column.vue

```html
<template>
  <div class="column">
    <div class="column-name">{{ column.name }}</div>
    <div class="ticket-list">
      <draggable v-model="tickets">
        <Ticket v-for="ticket in column.tickets" :ticket="ticket" :key="ticket.id"></Ticket>
      </draggable>
    </div>
  </div>
</template>
```

拖拉的視覺效果就出現了～

![](https://i.imgur.com/FxsmAsd.png)


然而現在還不能把ticket拖到原本column以外的其他column...(那我的ticket不就不能拖到done欄位了!?)

要怎麼尋找解答呢？

## 重點：參考`draggable`文件的`two list`source code，研究如何讓ticket拖到其他的column

我們從[這段](https://github.com/SortableJS/Vue.Draggable/blob/master/example/components/two-lists.vue)文件發現`two-lists.vue`的這個範例發現，範例裡面只要設`group="people"`的兩個list就可以互相拖拉:

```javascript
<template>
  <div class="row">
    <div class="col-3">
      <h3>Draggable 1</h3>
      <draggable class="list-group" :list="list1" group="people" @change="log">
        <div
          class="list-group-item"
          v-for="(element, index) in list1"
          :key="element.name"
        >
          {{ element.name }} {{ index }}
        </div>
      </draggable>
    </div>

    <div class="col-3">
      <h3>Draggable 2</h3>
      <draggable class="list-group" :list="list2" group="people" @change="log">
        <div
          class="list-group-item"
          v-for="(element, index) in list2"
          :key="element.name"
        >
          {{ element.name }} {{ index }}
        </div>
      </draggable>
    </div>

    <rawDisplayer class="col-3" :value="list1" title="List 1" />
    <rawDisplayer class="col-3" :value="list2" title="List 2" />
  </div>
</template>
```


所以我們也來把每個ticket的group設定為同樣名稱，都叫做`column`，這樣移動到哪一欄都可以：

```
<draggable v-model="tickets" group="column" @change="dragTicket">
  <Ticket v-for="ticket in column.tickets" :ticket="ticket" :key="ticket.id"></Ticket>
</draggable>
```


註：這裡有個奇妙的雷，`draggable`元件如果是使用大寫<Draggable>註冊元件，就沒有辦法在兩個column拖拉之間啊呵呵呵！這個bug我找了20分鐘...


## Step 2. 監聽`@change`事件，寫一個callback function`dragTicket`更新ticket移動位置

先把這個`@change`觸發的`cardMoved`function的event印出來，看有什麼參數可以利用

```
<script>

  import Rails from '@rails/ujs';
  import Ticket from "components/kanban/ticket"
  import draggable from 'vuedraggable';

  export default {              
    name: 'Column',
    // 讓用元件的父層，餵資料給子元件    
    props: ["column"],
    components: { Ticket, draggable },    
    // 每個元件都有自己的狀態和資料，所以data需要給一個能return值回去的function 
    data: function () {
      return {
        // `v-for`就可以改成用tickets跑迴圈
        tickets: this.column.tickets   
      }
    },
    methods: {
      dragTicket(evt){
        console.log(evt)
      }
    }
  }
</script>
```

從console印出的結果，我們發現有`added`和`removed`事件可以使用
但是如果那張`ticket`只是移動在自己原本在的同一欄，就只有`moved`事件

![](https://i.imgur.com/0mXrW6Q.gif)

為了確定移動的`ticket`是哪張，可以把element的id存起來，並再次用console印出來看看

```
dragTicket(evt){
  console.log(evt)
  let ticketShifted = evt.added || evt.moved
  if(ticketShifted){
    let ticket_id = ticketShifted.element.id
    console.log(ticket_id)
  }
}
```

![](https://i.imgur.com/WgeJZBR.gif)

## Step 3. routes新增路徑，controller新增action

這段的步驟就跟昨天的`column`拖拉步驟很類似，主要為了打API做準備。
我們希望設計出拖拉後的tickets路徑為
`/kanbans/:kanban_id/tickets/:id/drag`
動詞為`PUT`

routes
```
  resources :tickets, only: [:create, :update, :destroy] do
    member do
      put :drag
    end
  end
```

tickets_controller
```
  def drag
    @card.update(card_params)
    render 'show.json'
  end
```

## 4. 移動完後，把API打到後端

我的column component現在的樣子：

column.vue
```javascript

<script>
  import Rails from '@rails/ujs';
  import Ticket from "components/kanban/ticket"
  import draggable from 'vuedraggable';

  export default {              
    name: 'Column',
    // 讓用元件的父層，餵資料給子元件    
    props: ["column"],
    components: { Ticket, draggable },    
    // 每個元件都有自己的狀態和資料，所以data需要給一個能return值回去的function 
    data: function () {
      return {
        // `v-for`就可以改成用tickets跑迴圈
        tickets: this.column.tickets   
      }
    },
    methods: {
      dragTicket(evt){
        console.log(evt)
        let ticketEvt = evt.added || evt.moved
        if(ticketEvt){
          let ticket_id = ticketEvt.element.id;
          let data = new FormData();
          data.append("ticket[column_id]", this.column.id)                  
          data.append("ticket[position]", ticketEvt.newIndex + 1)

          Rails.ajax({
            url: `/kanbans/${this.column.kanban_id}/tickets/${ticket_id}/drag`,
            type: 'PUT',
            data,
            dataType: 'json',
            success: result => {
              console.log(result);
            },
            error: error => {
              console.log(error)
            }
          });
        }
      }
    }
  }
</script>

```

`拖拉ticket功能`，DONE!  
  
![](https://i.imgur.com/VRQURSr.png)



備註：有興趣研究Rails x Vue.js專案的拖拉部分的人，可以參考[GoRails: Rails & Vue.js Trello Clone](https://gorails.com/episodes/rails-vuejs-trello-clone-part-1)的影片，我的鐵人賽系列文章的靈感就是從這套影片來的，非常推薦！

Ref: 

 * [GoRails: Rails & Vue.js Trello Clone](https://gorails.com/episodes/rails-vuejs-trello-clone-part-1)
