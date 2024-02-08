---
title:  "第12屆鐵人賽Day 8 Vue元件裡的props屬性(1)"
preview: ""
permalink: "/articles/2020-09-21"
date:   2020-09-19 09:58:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

昨天鐵人賽提到了`component`元件是個堆積木的概念，讓我們可以更容易重複使用。結尾也提到了一個問題：在Vue裡面，不同的元件們該如何傳遞資料？
<!-- more -->

在我們設置的`Rails`專案中，`home.html.erb`這個首頁的外層有一個名為`Root`的父元件，
內層有`News`和`Foot`兩個子元件。

html: home.html.erb
```
<div id="content">
  <div class="main-title">Vue.js x Rails第12屆鐵人賽專案：{{ day }}</div>
  <div class="main-body">本日主題：{{ topic }}</div>
  <News></News>
  <Foot></Foot>
</div>
```

如何像下圖所示，利用`props`屬性，把`Root`元件裡的資料，向下傳給內層的`News`元件呢？

![](https://i.imgur.com/zezvqRT.png)



## Step1. 在`id`名為`content`的Vue Root掛載點，使用`author="Ting`的方式，將字串透過`author`傳入`News`Vue元件

html: home.html.erb
```
<div id="content">
  <div class="main-title">Vue.js x Rails第12屆鐵人賽專案：{{ day }}</div>
  <div class="main-body">本日主題：{{ topic }}</div>
  <News author="Ting"></News>
  <Foot></Foot>
</div>
```

## Step2. JS抓取Vue Root掛載點的id元素，建構一個Vue instance，並註冊News的Vue元件

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
        day: "第 8 天",
        topic: "元件的溝通 - props",
      },
      components: { News, Foot }
    })    
  }
})
```


## Step3. 設置props



我的`News`元件有兩個區塊，  
`message`代表著每一天我的不同鐵人賽的文章重點：內容會以鬍子語法傳入` { { message } }`。  
而`author`作者這筆資料，我需要接收Vue instance傳入的資料到`news`這個Vue元件的`props`屬性。  


news.vue
```
<template>
  <div class="news">
    <h1>本日重點：{ {  message } } </h1>
    <i>by  { { author } } </i>
  </div>
</template>

<script>
  export default { 
    props: ['author'],    
    data: function () {
      return {
        message: "由外層到內層元件的單向資料流：設定元件屬性props，可供news元件使用"
      }
    }
  }
</script>

<style scoped>
  .news{
    padding-left: 20px;
    background-color: rgb(248, 225, 194);
  }
  h1 {
    color: brown;
  }
</style>
```

以上，我們就可以把原本塞在`Root`的`author`的值傳入給下一層的`News`元件。
明天來介紹動態接收從Vue instance傳過去的值～





