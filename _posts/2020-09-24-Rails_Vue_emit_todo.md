---
title:  "第12屆鐵人賽Day 11 Vue元件單向傳遞: $emit 製作todo list"
preview: ""
permalink: "/articles/2020-09-24"
date:   2020-09-21 09:58:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

經過昨天介紹`$emit`可以傳遞自訂的事件給父層元件之後，今天來利用`$emit`的特性，設計一個~~有`3M`便利貼風格~~的to-do list吧!

![](https://i.imgur.com/kR9PrEd.png)

實作需求：

- **新增**  
我想要在`input`欄位裡，讓使用者從`Todos`元件輸入本日代辦事項，
按完`enter`送出後，把資料送去`Root`元件裡顯示。

- **刪除**  
點擊任一的`todo item`，就可以把item從`todo`陣列裡移除。

## Step0. Todos元件：設計`template`

把`input form`綁上雙向事件處理的`v-on`，讓使用者`enter`送出後呼叫`createItem`這個methods裡面的function。

## Step1. Todos元件的methods: `$emit('自訂事件名稱')`傳出事件

`createItem($event)`方法裡面帶了參數，讓我們可以把`$event.target.value`傳到上層的Vue Instance。

Todos.vue
```
<template id="todo-3M">
  <div class="todo-form">
      <label>本日To do list</label><i class="fas fa-pencil-alt" ></i><hr>
      <input class="todo-input" type='text' @keyup.enter="createItem" placeholder="請輸入您今天的代辦事項">
  </div>
</template>

<script>
  export default {              
    methods: {
      createItem($event){
        this.$emit("additem", $event.target.value)
        $event.target.value = ""
      }
    },
    template: "#todo-3M"
  }
</script>

<style scoped>
<!-- 略 -->
</style>
```

## Step2. 在Vue Root掛載點，接收傳出來的事件

Vue Root掛載點內的`<Todos>`元件，綁上雙向事件處理的`v-on`，
讓`<Todos>`元件傳遞出來的`additem`可以跟Root元件的`addMoreItem`綁定

home.html.erb
```
<div id="content">
  <div class="main-title">Vue.js x Rails第12屆鐵人賽專案：{{ day }}</div>
  <div class="main-body">本日主題：{{ topic }}</div>

  <Todos @additem="addMoreItem"></Todos>
  <ul class="todo-item">
    <li v-for="todo in todos" @click="removeItem(todo)"><i class="far fa-calendar-check"></i> {{ todo }} </li>
  </ul>
  
  <Foot></Foot>
</div>
```

## Step3. 父元件接收事件後，執行`addMoreItem()` function

我的`todos`陣列初始值預設有三件代辦事項。父元件接收事件之後，還可以再把其他代辦事項`push`進去這個陣列。

## Step4. `v-for`裡的todo迴圈，使用`@click`事件綁定`removeItem(todo)` function

若對單一`todo`item點擊，則透過javascript的`splice`方法，把該項todo item從陣列中移除。


home.js
```
import TurbolinksAdapter from 'vue-turbolinks'
import Vue from 'vue/dist/vue.esm'
import Todos from "../components/todos"
import Foot from "../components/foot"


Vue.use(TurbolinksAdapter)

document.addEventListener('turbolinks:load', () => {
  let el = document.querySelector("#content");

  if (el){
    new Vue({
      el,
      data: {
        day: "第 11 天",
        topic: "元件的由內而外傳遞事件 - emit",
        todos: ["買咖啡", "買口罩", "去郵局"],
      },
      methods: {
        addMoreItem(item){
          this.todos.push(item)
        },
        removeItem(item){
          let itemIndex = this.todos.indexOf(item)
          if (itemIndex >= 0){
            // 從array中刪除項目，並回傳被刪除後的項目
            this.todos.splice(itemIndex, 1)
          }
        }
      },
      components: { Todos, Foot }
    })    
  }
})
```


完成圖：
![](https://i.imgur.com/e7l7Jia.gif)

如果非父子元件之間傳遞，而是平行階層、或是跨階層的元件資料傳遞，
我們會透過Vue的event bus來實作，或者是以Vuex進行狀態管理。
這就是明後兩天要介紹的部份囉～請待下回分解！  


Ref: 
* [Vue英文官網手冊: Emitting a Value With an Event](https://vuejs.org/v2/guide/components.html#Emitting-a-Value-With-an-Event)  
* [書籍：Vue.js 2 前端漸進式建構框架實戰應用](https://books.google.com.tw/books/about/Vue_js_2%E5%89%8D%E7%AB%AF%E6%BC%B8%E9%80%B2%E5%BC%8F%E5%BB%BA%E6%A7%8B%E6%A1%86%E6%9E%B6%E5%AF%A6.html?id=i66aDwAAQBAJ&redir_esc=y)  
* [比較 keydown, keypress, keyup 的差異](https://medium.com/@yitailin/%E6%AF%94%E8%BC%83-keydown-keypress-keyup-%E7%9A%84%E5%B7%AE%E7%95%B0-4e873ba17e81) 
* [[JavaScript] slice()、splice()、split() ](https://medium.com/@bebebobohaha/slice-splice-split-%E5%82%BB%E5%82%BB%E5%88%86%E4%B8%8D%E6%B8%85-46d9c8992729) 