---
title:  "第12屆鐵人賽Day 13 Vuex狀態管理"
preview: ""
permalink: "/articles/2020-09-26"
date:   2020-09-23 09:58:00
layout: post
tags: 
  - "rail"
  - "vue"    
---


昨天有聊到`event bus`雖然方便，但是不容易偵錯及維護。
隨著專案變大、元件變得越來越多，元件間的架構越來越複雜，有時候會遇到多個元件需要共用同一份資料的情況...。

> Eg.某一訊息app的已讀/未讀，要如何在手機版、網頁版都能一致的顯示?

在大型的Vue.js專案通常會使用`Vuex`來進行狀態管理(State Management)，以實現了Single source of Truth(SSOT，單一真值來源)。當我們的來源只有一個的時候，寫的程式邏輯變少，出錯的機會也就降低了～

知名的前端框架都會有狀態管理(State Management)的設計，例如：  
[Vuex](https://vuex.vuejs.org/)之於Vue，  
[Redux](https://redux.js.org/)之於React、   
[NgRx](https://ngrx.io/guide/store)之於Angular。  

從[Vuex](https://vuex.vuejs.org/)官網上的video上可以看到這張解釋`Vuex`與`Vue`的差異。（推薦一定要去看影片！初學者也能夠快速理解state management的設計邏輯。）

**Vue和Vuex的相同點**  

左邊`Vue`，和右邊`Vuex`同樣都需要new一個instance出來。  
在Vuex裡，會把這個實體取名為`Store`，儲存元件中共享的資料狀態。  

![](https://i.imgur.com/LsQ5EtX.png)

| Vue | Vuex | 
| -------- | -------- | 
| `methods`可以更新`data`    | `action`可以更新`state`     |
| `computed`可以取得`data`的值   | `getters`可以取得`state`的值     |

(methods和computed的比較在[第六天鐵人賽]()的時候有介紹唷！)

**相異點**

然而，在`Vuex`，為了確保state的一致性，我們主要透過交給`mutations`這裡來追蹤、同步state的狀態，而讓action只用來呼叫`mutations`而已。（`mutation`這個單字就有變化、變異與轉換的意思～是不是能生動地表達跟action的差別呢？）

請注意：如果不通過mutation來改變state的話 ，狀態就不會被同步！所以請不要直接透過action去更新state。

![](https://i.imgur.com/khArS4O.png)

總結：在Vuex中，元件更動狀態流程如下(圖片來自[官網](https://vuex.vuejs.org/))：  

- Actions: 發出Commits呼叫Mutation。
- Mutations: 去更改 state 的方法。


![](https://i.imgur.com/QM0j3gM.png)


今天就來致敬一下官網提供的[記數器](https://jsfiddle.net/tingtinghsu/53a4cxsv/)範例，並且搬到我們的Rails App吧！



# 實作需求：

我想透過`Vuex`的狀態管理，在`Root`和`Counter`元件按`增+`或`減-`，都能同步顯示一樣的值。

![](https://i.imgur.com/uClp2qh.png)


# Step0. 安裝並引入`Vuex`

首先在Rails 6專案裡利用指令`yarn add vuex`安裝

```
yarn add vuex 
//略
...
success Saved lockfile.
success Saved 1 new dependency.
info Direct dependencies
└─ vuex@3.5.1
info All dependencies
└─ vuex@3.5.1
✨  Done in 3.30s.
```

# Step1. 寫一份`Store.js`的檔案

並且透過mutations的方式更改sate裡`count`的值

```
import Vue from 'vue/dist/vue.esm'
import Vuex from 'vuex'

Vue.use(Vuex)

// 定義一個新的 Vue Store
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    // 將state設定為參數
    increment: state => state.count++,
    decrement: state => state.count--
  }

})
export default store;
```

# Step2. 建立`Counter`元件

引入上一步驟的的`store.js`  
讓`+`,`-`按鈕可以被綁定在methods裡的`increment`與`decrement`函式  
並且透過`store.commit`送至`mutations`執行  

`computed`的`count`會根據data中的任何變化而立即重新計算  

counter.vue
```
<template>
  <div id="app">
    <!-- <p>123</p> -->
    <div class="main-counter">
    <p>Counter元件: Vuex計數器</p>
    <p class="display-count">{{ count }}</p>
    <p>
      <button class="counter-button" @click="increment">+</button>
      <button class="counter-button" @click="decrement">-</button>
    </p>
    </div>
  </div>
</template>

<script>
  import store from '../packs/store';
  export default {              
    store,
    computed: {
      // 直接當作普通屬性調用，不加括號
      // data中的任何變化會立即重新計算
      // 會有緩存
      count(){
        return store.state.count
      }
    },
    methods: {
      increment(){
        store.commit('increment')
      },
      decrement(){
        store.commit('decrement')
      }
    }  
  }
</script>
```

# Step3. 視實作的需求，我們也在其他的元件引入同一份`store.js`

例如：我想在Root元件也可以顯示`count`的記數，就可以透過`computed`return
這個store裡面的state值。

```
import Counter from "../components/counter"
import store from './store';

Vue.use(TurbolinksAdapter)

document.addEventListener('turbolinks:load', () => {
  let el = document.querySelector("#content");

  if (el){
    new Vue({
      store,
      el,
      data: {
        day: "第 13 天",
        topic: "Vuex: 狀態管理",
      },
      computed: {
        count(){
          return store.state.count
        }
      },
      methods: {
        increment(){
          store.commit('increment')
        },
        decrement(){
          store.commit('decrement')
        }
      },
      components: { Counter,Foot }
    })
  }
})
```

# Step4. 首頁的layout掛上`Counter`元件

home.html.erb

```
<div id="content">
  <div class="main-title">Vue.js x Rails第12屆鐵人賽專案：{{ day }}</div>
  <div class="main-body">本日主題：{{ topic }}</div>
  <div class="main-counter">
    <p>Root元件: Vuex計數器</p>
    <p class="display-count">{{ count }}</p>
    <p>
      <button class="counter-button" @click="increment">+</button>
      <button class="counter-button" @click="decrement">-</button>
    </p>
  </div>
  <Counter></Counter>
  <Foot></Foot>
</div>

```

完成圖：
![](https://i.imgur.com/QIewyfw.gif)  



後記：在理解Vuex的mutations時，讓我聯想到Rails專案裡，紀錄`schema`欄位變化的`migration`遷移檔，也是為了讓不同開發者的資料庫能夠一致，多拉出了一個層級，去統一管理資料欄位的變化。（不知道這樣的譬喻恰不恰當~XD 請各位前輩指教）

明天再來講一個大重點：Vue instance的生命週期！

Ref: 

* [Vuex官網](https://vuex.vuejs.org/) 

* [[Vue] Vuex 是什麼? 怎麼用? — State、Mutations (1/5)](https://medium.com/itsems-frontend/vue-vuex1-state-mutations-364163b3acac) 

* [[Vue.js] Vuex 學習筆記 (3) - 簡單應用](https://jeremysu0131.github.io/Vue-js-Vuex-%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98-3-%E7%B0%A1%E5%96%AE%E6%87%89%E7%94%A8/) 

* [MVC 架構演進 — Single Source of Truth](https://medium.com/@rayshih771012/mvc-%E6%9E%B6%E6%A7%8B%E6%BC%94%E9%80%B2-single-source-of-truth-2720f5a5facd) 

* [[ngrx/store-1] 有關前端的狀態管理](https://ithelp.ithome.com.tw/articles/10191884) 

