---
title:  "第12屆鐵人賽Day 27 Rails專案開發 - 編輯ticket (使用vuex狀態管理)"
preview: ""
permalink: "/articles/2020-10-10"
date:   2020-10-06 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

前天我們完成了`create ticket`，昨天也順便介紹了`v-if`語法；
今天就延續著介紹以Vue.js實作`CRUD`的編輯功能！

# Step 1. 設計編輯區塊

首先設計按下`編輯`的那枝筆的icon，會出現更新的小區塊，如下圖所示：
![](https://i.imgur.com/1nLui9G.png)

我們先用昨天的`v-if`語法來現學現賣，  
讓`編輯icon`會綁一個`v-on`語法，設計按下後出現的toggle開關`editTicket`為`true`，  
如果是`true`的話，再讓`v-if="editTicket"`的編輯區塊全部秀出來：

```
<template>
  <div class="ticket">
    {{ ticket.name }}
    <div>
      <button class="edit-btn" @click="editTicket=true"><i class="fas fa-edit text-gray-400"></i></button>

      <div v-if="editTicket" class="edit-area">
        <i class="far fa-window-close edit-cancel" @click="cancelUpdate"></i> 
        <textarea type="text" class="edit-input" v-model="ticket.name"></textarea>
        <button class="update-ticket-btn" @click="updateTicket">更新</button>
      </div>

    </div>
  </div>
</template>
```

當`editTicket`為`true`時，`edit-area`有兩個部分的按鈕也需要設計：

- 一個是`關閉視窗 far fa-window-close`的按鈕，按下時綁定`cancelUpdate` method，
- 另一個是`更新`按鈕時，綁定`updateTicket` method；

兩者都會觸發`editTicket = false`，也就是回到原來沒有編輯輸入框的畫面；

```
<script>
  import Rails from '@rails/ujs';
  export default {              
    name: 'Ticket',
    props: ["ticket"],
    data: function () {
      return {
        column: this.ticket.column,
        editTicket: false
      }
    },    
    methods: {
      cancelUpdate(evt){
        evt.preventDefault();
        this.editTicket = false;
      },
      updateTicket(evt){
        evt.preventDefault();
        this.editTicket = false;
      },
      deleteTicket(evt){
        //略
      }
    }  
</script>
```

綁上`v-model`後，只要我們一在輸入欄位更新資料，畫面上就會跟著連動～太神奇了傑克
![](https://i.imgur.com/UE0IWHW.gif)


## Step2. Vuex狀態管理 `this.$store.dispatch`

還記得Vuex的這張流程圖嗎？今天我們要實作的方式是怎麼由`component`觸發Vuex：

![](https://i.imgur.com/QM0j3gM.png)

在這裡我們為了要把`ticket component`裡面的`ticket id`和`ticket name`送到`Vuex`的同名action裡運用，
使用了`this.$store.dispatch()`的方式。`dispatch`這個英文字的中譯有`調度`、`分發`的意思，它實際上是一個觸發的方法。
`this.$store.dispatch(actionType, payload)`會傳兩個參數，一個是要觸發Vuex的action的類型，另一個是攜帶的資料（payload），

所以我們在Vue method可以這樣寫：
```
updateTicket(evt){
  evt.preventDefault();
  this.$store.dispatch("updateTicket", {id: this.ticket.id, name: this.ticket.name})
  this.editTicket = false;
},
```


# Step3. Vuex action 把資料往後端送

首先確定更新ticket的路徑及動詞，再請Rails打ajax到後端。

|  Helper   | HTTP Verb | Path | Controller#Action |
| --- | -------- | -------- | -------- |
|  kanban_ticket_path   | PUT     | /kanbans/:kanban_id/tickets/:id(.:format)     | tickets#update    |

剛剛從Component接來的`ticket id`和`ticket name`在這裡就可以派上用場了：

```
  actions: {
    updateTicket({ commit }, {id, name}){
      let data = new FormData();
      let el = document.querySelector("#column");        
      data.append("ticket[name]", name);
      Rails.ajax({
        url: `/kanbans/${el.dataset.kanbanid}/tickets/${id}`,
        type: 'PUT',
        data,
        dataType: 'json',
        success: result => {
          // 步驟5會提到！
          commit("UPDATE_TICKET", result);
          console.log(result);
        },
        error: error => {
          console.log(error);            
        }
      });      
    },

    dragColumn({ commit, state }, evt) {
      //略
    },
    fetchColumn({ commit }, kanbanid){
      //略
    }
  }
```

## Step 4. MVC: controller 更新資料庫

因為ticket的內容已經更新，我們透過rails controllerrender新的show.json

tickets_controller.rb
```
def update
  respond_to do |format|
    if @ticket.update(ticket_params)
      format.json { render :show, status: :ok}
    else
      format.json { render json: @ticket.errors, status: :unprocessable_entity }
    end
  end
end
```

## Step 5. ajax回傳結果為成功的話，送出commit請Mutation更新state

資料庫更新之後，畫面上也要請mutations進行變更。
所以我們在Vuex的store裡 `commit("UPDATE_TICKET", result);`，會觸發`UPDATE_TICKET`變更state。

更新ticket的方式，是透過`state.columns`的findIndex，比對傳進來的ticket的`id`和`column_id`，找到這張ticket在哪一欄位、哪一個序號，並且透過`splice`把內容更新上去。

```
  mutations: {
    UPDATE_COLUMNS(state, columns){
      state.columns = columns;
    },

    UPDATE_TICKET(state, ticket){
      let column_index = state.columns.findIndex(col => col.id == ticket.column_id)
      let ticket_index = state.columns[column_index].tickets.findIndex(tkt => tkt.id == ticket.id)

      state.columns[column_index].tickets.splice(ticket_index, 1, ticket)
    }
  },
```


編輯ticket，完成！而且重新refresh仍然是更新後的ticket

![](https://i.imgur.com/tOJcVhA.gif)


小感言：  
Rails的CRUD果然是強項呀！相比之下Rails可以20分鐘搞定；
前端+Vue.js新手，我用js刻功能的話連簡單的CRUD都要做好幾天...

再三天就鐵人賽結尾了，不屈不撓堅持到底！

