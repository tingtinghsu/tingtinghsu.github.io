---
title:  "第12屆鐵人賽Day 15 Vue instance 的 mounted()"
preview: ""
permalink: "/articles/2020-09-28"
date:   2020-09-23 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

昨天在理解Vue的生命週期時提到，Vue在前端處理上有一個透過掛載`mounting`的hook處理`畫面`渲染至瀏覽器前的階段。所以我們今天試著在Rails App裡來使用`mounted()`這個hook實作需求。

<!-- more -->

# 本日實作需求：

在做網站的時候通常會需要一些假資料（例如虛擬使用者的`name`，`email`，`avatar`頭像等等），以幫助我們更容易去想像畫面及實際開發功能。
而網路上有一些不錯的第三方API，可以幫助我們創建這些假資料～

我想透過`axios`從遠端伺服器取得的資料把[這個 Face API](https://tinyfac.es/)內的使用者`avatar`頭像畫面，透過`mounted()`渲染在前端頁面上！
(今天就不掛其他的元件了，把重點focus在理解life cycle hook上)  

大概4醬子的感結～～  
![](https://i.imgur.com/0nEo5Ip.png)

# 什麼是`axios`?

axios是Vue官方推薦的非同步工具，使用`get`、`post`、 `push`等Restful(Representational State Transfer: 表現層狀態轉換)規範來取得第三方資料，`.then`表示取得資料後的處理動作。

常用的方法如下:

```
axios.request
axios.get
axios.delete
axios.head
axios.post
axios.put
axios.patch
```

(查資料時發現，另一個前端框架`Angular`本身有提供了一個[簡化的 HTTP 客戶端 API](https://angular.tw/guide/http) `$http`，而axios就是之於Vue的類似角色。)

開始進行吧！

# Step 0. 使用`yarn add`安裝並 `import` `axios-on-rails`

```
yarn add axios-on-rails

info Direct dependencies
└─ axios-on-rails@0.0.1
info All dependencies
├─ axios-on-rails@0.0.1
├─ axios@0.17.1
└─ rails-ujs@5.2.4
✨  Done in 5.22s.
```


# Step 1. 在`mounted()` hook透過`axios`取得資料

在Vue instance的`data`選項物件，建立一個`subscribers: []`的空陣列  
透過`axios.get`取得資料後放入陣列

home.js
```
import TurbolinksAdapter from 'vue-turbolinks'
import Vue from 'vue/dist/vue.esm'
import Foot from "../components/foot"
import store from './store';
import axios from 'axios-on-rails'


Vue.use(TurbolinksAdapter)


document.addEventListener('turbolinks:load', () => {
  let el = document.querySelector("#content");

  if (el){
    new Vue({
      store,
      el,
      data: {
        day: "第 14 天",
        topic: "Vue instance的 mounting 階段 mounted()： el指定的標籤已經掛載完成",
        audience: "訂閱這個鐵人賽系列文的觀眾：",
        subscribers: []
      },
      mounted() {
        var vm = this
          axios.get('https://tinyfac.es/api/users')
            .then((response) => {
                vm.subscribers = response.data
            })
      },
      components: { Foot }
    })
  }
})
```

這時console會出現錯誤訊：

![](https://i.imgur.com/9mTdZQz.png)

## Step 2. 解決使用API時，跨網域存取被拒絕問題

在前端開發時使用`ajax`、`axios`、`fetch`等HTTP Request來存取API資料時有時會遇到CORS(跨來源資源共用，Cross-Origin Resource Sharing)問題，導致無法正常讀取API內容。別擔心！在此使用`cors-anywhere`就可以順利解決。  

程式碼稍加改寫一下:

```
document.addEventListener('turbolinks:load', () => {
  let el = document.querySelector("#content");
  const cors = 'https://cors-anywhere.herokuapp.com/';
  const url = 'https://tinyfac.es/api/users';

  if (el){
    new Vue({
      store,
      el,
      data: {
        day: "第 14 天",
        topic: "Vue instance的 mounting 階段 mounted()： el指定的標籤已經掛載完成",
        audience: "訂閱這個鐵人賽系列文的觀眾：",
        subscribers: []
      },
      mounted() {
        var vm = this
        // axios.get('https://tinyfac.es/api/users')
        axios.get(`${cors}${url}`)
            .then((response) => {
                vm.subscribers = response.data
            })
      },
      components: { Foot }
    })
  }
})
```

## Step 3. 在Vue instance掛載點中，使用`v-for`把`avatar`頭像印出來

[這個API](https://tinyfac.es/api/users)裡有好大一包`json`檔，研究資料結構後發現取出`img`的`url`方式是`user.avatars[0].url`。
所以我們透過`v-for`，將每位user跑迴圈，並且透過`v-bind`將資料綁定HTML標籤屬性(不熟的看倌請複習[第5天的鐵人賽](https://ithelp.ithome.com.tw/articles/10239655))，`img`這個標籤裡的`src`網址與`user.avatars[0].url`做連動～

```
<div id="content">
  <div class="main-title">Vue.js x Rails第12屆鐵人賽專案：{{ day }}</div>
  <div class="main-body">本日主題：{{ topic }}</div>
  <div class="main-audience">{{ audience }}</div>  
  <div class="main-avatar" v-for="user in subscribers">
    <img class="avatar" :src="user.avatars[0].url">
  </div>
  <Foot></Foot>
</div>
```

備註：avatar的css可以設定為圓形～以符合一般社群網站頭像的顯示風格;)！

```
  .avatar{
    float: left;
    margin-top: 1em;
    margin-right: 1em;
    position: relative;
    height: 50px;
    width: 50px;

    -webkit-border-radius: 50%;
    -moz-border-radius: 50%;
    border-radius: 50%;
  }
```

頭像順利印出來啦！
![](https://i.imgur.com/0P4dfiA.png)

經過半個月與Vue的基礎相處～好像有開始體會到前端框架的有趣之處！
接下來從第16天到第30天，我們要來開發透過資料庫實現功能的Rails網站 feat Vue.js了！

Ref: 

* [如何解決跨網域存取被拒絕問題](https://andy6804tw.github.io/2019/09/21/fix-cors-problem/#%E5%A6%82%E4%BD%95%E8%A7%A3%E6%B1%BA%E8%B7%A8%E7%B6%B2%E5%9F%9F%E5%AD%98%E5%8F%96%E8%A2%AB%E6%8B%92%E7%B5%95%E5%95%8F%E9%A1%8C)  

* [CORS anywhere](https://cors-anywhere.herokuapp.com/)  

* [How to loop trough an array of images within an array in vue js](https://stackoverflow.com/questions/54358631/how-to-loop-trough-an-array-of-images-within-an-array-in-vue-js)  



