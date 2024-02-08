---
title:  "第12屆鐵人賽Day 10 Vue元件裡由內而外的事件單向傳遞: $emit"
preview: ""
permalink: "/articles/2020-09-23"
date:   2020-09-20 09:58:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

元件內的資料傳遞可以說是`Vue`的大重點之一。
前兩天介紹了由外層父元件傳給子元件，
而今天要來說說如何把子元件由內到外進行「事件的單向傳遞」傳給父元件的Vue instance。

來！先跟我一起記一下口訣～
- 由外而內`props`
- 由內而外`emit`

以下是今天在Rails app練習時做出的頁面：
當我按下子元件的`按我，送出Emit`按鈕的時候，會將`this.$emit('news');`送出去。
![](https://i.imgur.com/epZuqU0.jpg)

## Step1. 子元件的methods: `$emit('自訂事件名稱')`

在UI使用Vue元件時，透過`@click='自訂事件名稱'`的事件綁定，
按鈕被按下後，會`emit`傳遞自訂的`news`事件給父層元件。

News.vue
```
<template>
  <div class="news">
    <h1>本日重點：{{ message }}</h1>
    <button class="news-btn" @click="clickNews">按我，送出Emit</button>
  </div>
</template>

<script>
  export default {   
    data: function () {
      return {
        message: "由內層到外層元件的單向資料流：在子元件利用$emit('自訂事件名稱')送出事件給父元件"
      }
    },
    methods: {
      clickNews(){
        this.$emit('news');
      }
    }
  }
</script>

```

## Step2. 在Vue Root掛載點，接收傳出來的事件

我在`News`元件上使用`<News v-on:news="rootMethod"></News> `語法，讓父層接收了子元件傳出的事件方法`news`

home.html.erb
```
<div id="content">
  <div class="main-title">Vue.js x Rails第12屆鐵人賽專案：{{ day }}</div>
  <div class="main-body">本日主題：{{ topic }}</div>

  <News v-on:news="rootMethod"></News>    
  <Foot></Foot>
</div>
```

## Step3. 父元件接收事件後，執行`rootMethod()`方法

在本日的鐵人賽是透過`console.log`印出`子元件送出事件，傳給父元件`這段話作為例子～
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
        day: "第 10 天",
        topic: "元件的溝通 - emit",
      },
      methods: {
        rootMethod(){
          console.log("子元件送出事件，傳給父元件")
        }
      },
      components: { News, Foot }
    })    
  }
})
```
emit完成圖
![](https://i.imgur.com/klPhtLa.png)

明天來用`$emit`的特性，做個簡單的to-do list吧！：）

Ref: 

* [Vue英文官網手冊: Emitting a Value With an Event](https://vuejs.org/v2/guide/components.html#Emitting-a-Value-With-an-Event)  
* [Vue 小學堂 資料傳遞Part1.父子組件的資料傳遞Props與Emit](https://medium.com/anna-hsaio-%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC%E8%A8%98/vue-%E5%B0%8F%E5%AD%B8%E5%A0%82-%E8%B3%87%E6%96%99%E5%82%B3%E9%81%9Epart1-%E7%88%B6%E5%AD%90%E7%B5%84%E4%BB%B6%E7%9A%84%E8%B3%87%E6%96%99%E5%82%B3%E9%81%9Eprops%E8%88%87emit-86002e58185ft)  