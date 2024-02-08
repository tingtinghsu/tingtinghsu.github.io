---
title:  "第12屆鐵人賽Day 9 Vue元件裡的props屬性(2)"
preview: ""
permalink: "/articles/2020-09-22"
date:   2020-09-20 09:58:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

昨天提到`props`屬性可以將外層元件的資料，傳遞到內層元件。
舉的例子是傳入特定的`author`字串給`News`元件，

但是一般來說的網頁使用情境，不太可能會把資料寫死在標籤裡。  
比較常見的是讓`使用者`自己輸入，或是透過`後端資料庫`或`API`傳資料進來。  


## 傳入子元件的資料是`變數`

在下列例子，我們希望`News`這個子元件會動態接收從Vue instance傳過去父元件姓名的值～並在子元件裡把觀眾的暱稱印出來。
該如何實作呢？  

我們可以參考[第5天]鐵人賽的範例修改、透過可以傳給子元件的props屬性把變數的值成功傳到子元件！

![](https://i.imgur.com/3clYxyQ.png)

## Step1. 在`id`名為`content`的Vue Root掛載點，利用`v-bind`綁定變數

昨天在子元件內是傳入無法修改的作者名稱：
`<News author="Ting"></News>` 
今天我想換成可以讓使用者輸入的暱稱欄位
`<News v-bind:nickname="name"></News>`  
並且跟`input`輸入值的做連動

html: home.html.erb
```
<div id="content">
  <div class="main-title">Vue.js x Rails第12屆鐵人賽專案：{{ day }}</div>
  <div class="main-body">本日主題：{{ topic }}</div>

  <News v-bind:nickname="name"></News>

  <div class="content-form">
    <div class="field is-horizontal">
      <div class="field-body">
        <div class="field">
          <p class="control">
            <input class="input" type="text" placeholder="你的暱稱" v-bind:nickname="name" v-model="name">
          </p>
        </div>
      </div>
    </div>
    <button class="button is-primary is-light" v-on:click="thankyou">送出</button>
  </div>    
  <Foot></Foot>
</div>
```

第五天曾經介紹過雙向事件處理時會使用到`v-model`以及`v-on`，在此一併加入例子做練習～

```
<!-- 表單輸入框 -->
  <input class="input" type="text" placeholder="你的暱稱" v-bind:nickname="name" v-model="name">
<!-- 按鈕 -->
  <button class="button is-primary is-light" v-on:click="thankyou">送出</button>
```


## Step2. JS抓取Vue Root掛載點的id元素，建構一個Vue instance並新增data屬性`name`，並預設為空字串。

home.js
```
import TurbolinksAdapter from 'vue-turbolinks'
import Vue from 'vue/dist/vue.esm'
import News from "../components/news"
import Foot from "../components/foot"


Vue.use(TurbolinksAdapter)

document.addEventListener('turbolinks:load', () => {
  let el = document.querySelector("#content");

  if (el){
    new Vue({
      el,
      data: {
        day: "第 9 天",
        topic: "元件的溝通 - props (2)",
        name: ''
      },
      methods: {
        thankyou(){
          alert( `${this.name}，謝謝你收看Vue x Rails 鐵人賽系列!`)
        }
      },
      components: { News, Foot }
    })    
  }
})

```

## Step3. 設置props

props的設置有兩種方式，`array`陣列以及javascript的`object`物件。在此例是先來個簡單的`array`:

```
<template>
  <div class="news">
    <h1>本日重點：{ {  message } } </h1>
    <i>觀眾: { { nickname } } </i>
  </div>
</template>

<script>
  export default { 
    props: ['nickname'],    
    data: function () {
      return {
        message: "由外層到內層元件的單向資料流：設定元件屬性props，並傳入變數"
      }
    }
  }
</script>
```

大功告成，變數成功地傳入`News`子元件囉。

完成圖
![](https://i.imgur.com/WR8NFb0.png)

這兩天了解了「由外到內」～明天要來談談如何「由內層元件到外層元件」進行的單向事件傳遞！