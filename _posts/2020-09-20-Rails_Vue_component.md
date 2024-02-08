---
title:  "第12屆鐵人賽Day 7 Rails專案內加上Vue元件"
preview: ""
permalink: "/articles/2020-09-20"
date:   2020-09-18 09:57:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

經過了三天的Vue簡單起手式，接著我們要回到Rails專案內實作了。（捲起袖子）  
今天來了解一下元件的概念！

<!-- more -->

# 元件`component`：堆樂高積木的概念

在開發的時候，管理團隊專案內JavaScript檔案一直是一件充滿挑戰的任務。自從框架帶來了元件`components`的概念後，重複使用的功能可以獨立出來成為一個一個可以重複使用的「積木」，讓跟特定區塊有關的`html`、`css`及`JavaScript`都集中在同個component。程式更好維護、更易於重複使用，也更不容易出錯了～

示意圖：元件就像樂高積木一樣
![](https://i.imgur.com/X5Eu0jM.png)
[(pic credit:)](http://www.sccnn.com/)


我們可以想像Vue的每個元件都是一個UI，各自具有`template`標籤(html)、`state`狀態(js)及`style`樣式(css)
![](https://i.imgur.com/TJaaKEH.png)
[(pic credit:)](https://vuejs.org/v2/guide/components.html)

每個Vue元件的架構，固定寫法長這樣（可以把這段程式碼在你喜愛的編輯器裡存成snippet～開發更快速唷）：
```javascript
<template>
// 這裡放html
</template>

<script>
// import Another from "/components/another.vue";

export default {
  name: '',
  data: function () {
    return {
    };
  },
  // components: { Another },
};
</script>

<style scoped>
// 這裡放css
</style>
```
特別注意的是，在component裡的`data`屬性，需要是一個有return值的function，每次回傳該元件專屬的狀態及資料。

# 註冊元件

當我們想要使用某一塊積木（例如上述程式碼註解的部分`Another`這塊積木時候），必須先`import`引入積木的位置、然後再來到`components: { Another }`這個位置註冊，根據慣例，元件名字的開頭大寫。

# Rails專案示範：掛`component`

學習新主題的時候，我習慣先來個最小可行解試試手感：

如下面Gif圖所示，當我在Rails專案某一個頁面的`Root`元件裡有一塊`footer`區域，要怎麼把這一塊改成`component`呢？

![](https://i.imgur.com/TJRIlz0.gif)

首先：在 html規劃好架構
`pages#home.html.erb`
```html
<div id="main-area">
  <div class="main-title">Vue.js x Rails{{ title }}</div>
  <div class="main-body">本日主題：Rails專案內加上Vue元件</div>
  <Foot></Foot>
</div>
```

我的`Root`元件：`main-page.js`:

```javascript
import TurbolinksAdapter from 'vue-turbolinks'
import Vue from 'vue/dist/vue.esm'
import Foot from '../components/foot'


Vue.use(TurbolinksAdapter)

document.addEventListener('turbolinks:load', () => {
  let el = document.querySelector("#main-area");

  if (el){
    new Vue({
      el,
      data: () => {
        return {
          title: " 第12屆鐵人賽專案"
        }
      },
      components: { Foot }
    })    
  }
})
```

`/frontend/components/foot.vue`

接著，就是~推積木時間~要產生一塊元件了～
在`foot`這塊元件的template用第四天介紹的`鬍子語法`把我的版權宣告內容傳進來。
然後在`style scoped`稍微調了一下css，這個`scoped`代表元件區域內的css不會影響到其他地方的css喔！便利ですね〜

```vue
<template>
  <div class="footer">
    <div class="footer-content">{{ trademark }}</div>
  </div>
</template>

<script>
export default {
  name: '',
  data: function () {
    return {
      trademark: "by Ting Ting. All rights reserved."
    };
  }
};
</script>

<style scoped>
  .footer{
    background-color: rgb(252, 252, 218);
    width: 100%;
    height: 20vh;
    position:absolute;
    bottom:0px;
    left:0px;
    font-size: 10px;
  }
  .footer-content{
    padding-top: 60px;
    text-align: center;
  }
</style>
```



成功地在Rails首頁的掛好第一個元件囉！
![](https://i.imgur.com/1OHifbR.png)

但現在有一個大哉問：

如果以後我們有了多個各自獨立的元件之後，不同的Vue元件該如何傳遞與分享資料呢？
明天要來深入研究元件資料的props屬性：）

Ref: 

* [Vue英文官網手冊: Organizing Components](https://vuejs.org/v2/guide/components.html)  