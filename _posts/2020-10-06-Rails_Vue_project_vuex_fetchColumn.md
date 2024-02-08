---
title:  "第12屆鐵人賽Day 23 Rails專案開發 - Vuex狀態管理 (1)"
preview: ""
permalink: "/articles/2020-10-06"
date:   2020-10-03 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

昨天我把一張自己開給自己的票：`拖拉ticket`解決了!  

接著換下一張票、同時也就是今天的鐵人賽主題：改寫成Vuex來管理狀態～

![](https://i.imgur.com/oyWzVVa.png)

在鐵人賽第13天的時候曾經聊到Vuex狀態管理，當我們的專案發展規模越來越大，元件越來越多，如何讓資料在跨元件之間溝通就是一件非常重要的事情。
(如果一直讓子元件用`$emit`傳給父元件、父元件再用`props`傳給其他子元件，這樣來來回回也太辛苦了吧～～～)

這時候，我們會使用Veux去產生一個用來管理網站全域的`Shared State`（共享狀態）的實體，而這個實體我們通常會把它取名叫做`store`(英文意思為倉庫)。

參考官網影片介紹的Vuex架構，今天我們就來把原本元件裡的`data`（如下圖左側）拉出來到`store`（下圖右側）獨立管理吧！  

![](https://i.imgur.com/khArS4O.png)  


# Step0. 安裝並引入`Vuex`

首先在Rails 6專案裡利用指令`yarn add vuex`安裝，這在第13天已經進行過了～

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

# Step1. new出Vuex的實體


在今天的任務，要從Vue元件裡的`data`拉出來、挪到`store`獨立管理的`State`叫做`columns`，先跟之前的元件寫法一樣，預設是一個空陣列。

如果元件想要使用`State`的話，是使用`Getters`屬性取值(`state`為`Getter`的第一個參數)

參考官網教學影片的`Store`流程圖：  
  
![](https://i.imgur.com/21shJU0.png)

我們寫在stores/column.js的路徑下的Vuex元件長得像這樣：

```
import Vue from 'vue/dist/vue.esm'
import Vuex from 'vuex'

Vue.use(Vuex)

// 定義一個新的 Vue Store
export default new Vuex.Store({
  state: {
    // 預設空陣列
    columns: []
  },
  getters:{ 
    columns: state => state.columns
  },
  mutations: {
    // 任何更改columns的值的行為都必須透過mutation
    UPDATE_COLUMNS(state, columns){
      state.columns = columns;
    }
  },
  actons: {
  }
});
```

# Step2. application.js 引入 `Vuex`的store

剛剛寫完熱騰騰`的column.js`趕快引入到原本的component：

`import store from 'stores/column'`

並且在new Vue裡面應該再加上`store`（import後就可以使用）

```
import Vue from "vue/dist/vue.esm";
import Column from "components/kanban/column"
import Rails from '@rails/ujs'
import Draggable from 'vuedraggable';
import store from 'stores/column';

document.addEventListener("turbolinks:load", () => {
  let el = document.querySelector("#column");
  if (el){
    new Vue({
      el,
      store, // import後就可以使用store
      data: {
        kanban_id: el.dataset.kanbanid,
        // columns: []   => 此時已經把column資料挪到Vuex裡了，原本的Vue可以把此段移除
      },
      components: { Column, Draggable },
      methods: {
       // 略
      },
      beforeMount(){
        // 略
      }
    });
  }
})
```
而Veux最大的重點是：之後有任何想要進行修改`State`的動作，都必須要透過`Actions`提交commit給`Mutations`。
（至於`Actions`在今天的需求裡該怎麼撰寫呢？稍安勿躁，請繼續往下看～）

# Step3. 思考：Column的Actions

之前在講解`column`元件時，有帶到生命週期的`beforeMount()`，在這個lifecycle hook會在畫面渲染至瀏覽器前的階段，把打API的結果更新至`column`陣列。
所以你應該猜到了～我們接下來要把以下這段`beforeMount`改寫進`Actions`：
```
beforeMount(){
  Rails.ajax({
    url: `/kanbans/${this.kanban_id}/columns.json`,
    type: 'GET',
    dataType: 'json',
    success: result => {
      this.columns = result;
    },
    error: error => {
      console.log(error);            
    }
  });
}
```

![](https://i.imgur.com/QM0j3gM.png)


以上是Vuex作用的流程圖，為了透過`Actions`提交commit給`Mutations`， 

我們把上一段搬移進去`Vuex.Store`的columns.js，  

寫一個名為`fetchColumn()`的function，參數有`commit`，以及打後端API路徑需要知道的`kanbanid`

```
  actions: {
    fetchColumn({ commit }, kanbanid){
      Rails.ajax({
        url: `/kanbans/${kanban_id}/columns.json`,
        ///API路徑：kanbans/2/columns.json

        type: 'GET',
        dataType: 'json',
        success: result => {
          commit("UPDATE_COLUMNS", result);
          //console.log(result);

        },
        error: error => {
          // console.log(error);            
        }
      });    
    }
  }

```

以下是專案的程式碼截圖：
左邊代表原本的`Vue`寫法，右邊是改寫為`Vuex`
主要把beforeMounted的code移動到`actions`來，再透過`commit`給`mutation`把資料寫回去：

![](https://i.imgur.com/EqeZt1o.png)

是不是跟Vuex官網的架構有87%像啊？（所以說～～官網要認真看！）  
![](https://i.imgur.com/khArS4O.png)  


改寫完`Vuex`之後，點擊`Kanban`，可以順利載入Column以及所屬的`ticket`。

於是我們又可以把這一張票：`改寫成vuex管理狀態`移到`DONE`了 :)

![https://ithelp.ithome.com.tw/upload/images/20201005/20111177YYgjPkvnRa.png](https://ithelp.ithome.com.tw/upload/images/20201005/20111177YYgjPkvnRa.png)

小提醒：
- 寫`javascript`可以善用`console log`把接到的資料印出來看看，加快開發速率喔！
- `Vue devtool`也要隨時開著，看有沒有紅字錯誤或警告訊息～

Ref: 

* [Vuex中文官網: Mutation](https://vuex.vuejs.org/zh/guide/mutations.html)  

* [Vuex中文官網: Getter](https://vuex.vuejs.org/zh/guide/getters.html)  

* [Vuex官網](https://vuex.vuejs.org/) 

* [GoRails: Rails & Vue.js Trello Clone](https://gorails.com/episodes/rails-vuejs-trello-clone-part-1)

* [五倍紅寶石學院: I'm coding it 向Trello致敬](https://campus.5xruby.tw/courses/) 