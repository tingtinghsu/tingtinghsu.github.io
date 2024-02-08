---
title:  "第12屆鐵人賽Day 28 Rails專案開發 - 刪除ticket (使用vuex狀態管理)"
preview: ""
permalink: "/articles/2020-10-11"
date:   2020-10-09 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

`CRUD`新增、刪除、修改、顯示功能是一個完整的線上系統不可或缺的功能。
前天我們完成了`更新 ticket`，以及介紹了`dispatch`讓Vue可以聯絡到Vuex的action，
今天就延續著介紹以Vue.js實作`CRUD`的刪除功能！

## Step 1. UI: 刪除icon連動 `deleteTicket` method

我們移至`ticket.vue`這個ticket元件的檔案，
起手式來先用個`fontawesome`加一個可愛的垃圾桶，並且`v-on`綁定click事件`deleteTicket`：

```
<template>
  <div class="ticket">
    {{ ticket.name }}
    <div>
      <button class="edit-btn" @click="editTicket=true"><i class="fas fa-edit text-gray-400"></i></button>
      //垃圾桶在這
      <button class="delete-btn" @click="deleteTicket"><i class="fas fa-trash text-gray-400"></i></button>

      <div v-if="editTicket" class="edit-area">
        <i class="far fa-window-close edit-cancel" @click="cancelUpdate"></i> 
        <textarea type="text" class="edit-input" v-model="ticket.name"></textarea>
        <button class="update-ticket-btn" @click="updateTicket">更新</button>
      </div>

    </div>
  </div>
</template>
```

垃圾桶放完後，一邊來點點看，一邊思考如何撰寫`deleteTicket`method   

![](https://i.imgur.com/89YhG7q.gif)

刪除功能最重要的是要刪到對的東西！因此，為了確定有抓對那筆即將被刪除的ticket，先用console.log確認一下是不是正確的資料：

```
<script>
  export default {              
    name: 'Ticket',
    props: ["ticket"],
    data: function () {
      return {
      }
    },    
    methods: {
      deleteTicket(evt){
        event.preventDefault();
        if (confirm(`確定刪除"${this.ticket.name}" 嗎?`)){
          console.log("remove: ") 
          console.log(this.ticket.id)
          console.log(this.ticket.name)
        }

      }
    }
  }
</script>
```

## Step2. 使用Vuex狀態管理 `this.$store.dispatch`

昨天介紹了`dispatch`這個概念，讓我們在Vue元件裡可以呼叫到Vuex store instance中actions定義的方法。

請看目前程式碼的架構，左邊為Vue(ticket元件)、右邊為Vuex store。
![](https://i.imgur.com/mBSIwVQ.png)

我想要當點擊確認刪除後，能夠呼叫`action`中定義的deleteTicket方法，並且傳入ticket_id和column_id兩個參數。


### 2-1. 橘色框線：Vue元件裡的methods `despatch`至Vuex的action

```
methods: {
  cancelUpdate(evt){
    //略
  },
  updateTicket(evt){
    //略
  },
  deleteTicket(evt){
    event.preventDefault();
    if (confirm(`確定刪除"${this.ticket.name}" 嗎?`)){
      this.$store.dispatch("deleteTicket", {ticket_id: this.ticket.id, column_id: this.ticket.column_id})                
    }
  }
}
```

### 2-2. 黃色框線：Vuex actions裡的delete_column，呼叫mutations

確定刪除ticket的路徑及動詞，再請Rails打ajax到後端。

|  Helper   | HTTP Verb | Path | Controller#Action |
| --- | -------- | -------- | -------- |
|  kanban_ticket_path   | DELETE     | /kanbans/:kanban_id/tickets/:id(.:format)     | tickets#destroy    |

成功刪除資料庫內的ticket之後，
我們發出一個commit，請求mutation裡`DELETE_TICKET`重新前端渲染的資料，並帶入剛剛從Vue元件傳進來的ticket_id和column_id兩個參數。
```
  actions: {
    updateTicket({ commit }, {id, name}){
      //略
    },
    deleteTicket({ commit }, {ticket_id, column_id}){
      let el = document.querySelector("#column");   
      Rails.ajax({
        url: `/kanbans/${el.dataset.kanbanid}/tickets/${ticket_id}`,
        type: 'DELETE',
        dataType: 'json',
        success: result => {
          commit("DELETE_TICKET", {ticket_id, column_id});
        },
        error: error => {
          console.log(error)
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

### 2-3. 紅色框線：Vuex mutations裡的DELETE_TICKET

`DELETE_TICKET`：需要透過傳進來的`ticket_id`和`column_id`，去比對state裡的columns的index現在是第幾欄和第幾列，再透過`splice`把需要刪掉的那張ticket從columns陣列中移除。

(PS. 比較mutations裡的`UPDATE_TICKET`可以直接透過ticket實體去找自己的id以及reference的column_id，但`DELETE_TICKET`已經刪除了ticket實體，因此我們要從外面的元件帶進來column_id 和ticket_id來比對資料。)

```
  mutations: {
    UPDATE_COLUMNS(state, columns){
      state.columns = columns;
    },
    // Day 28
    DELETE_TICKET(state, {ticket_id, column_id}){      
      let column_index = state.columns.findIndex(col => col.id == column_id)
      let ticket_index = state.columns[column_index].tickets.findIndex(tkt => tkt.id == ticket_id)
      state.columns[column_index].tickets.splice(ticket_index, 1)
    },

    //Day 27
    UPDATE_TICKET(state, ticket){
      let column_index = state.columns.findIndex(col => col.id == ticket.column_id)
      let ticket_index = state.columns[column_index].tickets.findIndex(tkt => tkt.id == ticket.id)
      state.columns[column_index].tickets.splice(ticket_index, 1, ticket)
    }
  },
```

感想：原本以為CRUD裡，刪除總是那個最快可以做好的功能，但我昨天和今天大概花了10個小時才完全搞清楚箇中原理，把握出vuex的流程、如何傳參數、如何送commit給mutation～（以及解完自己產生的Bug 嗚嗚）

當ticket刪得掉時候真的很感動！

![](https://i.imgur.com/pIAKjFM.gif)

透過mutations去更動前端雖然看起來有點繞(dispatch=> actions => mutations)，但等到我們明天來實作Action cable，你就會發現好處多多～敬請期待明天！