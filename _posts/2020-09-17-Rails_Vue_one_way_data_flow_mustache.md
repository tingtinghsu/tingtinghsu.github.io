---
title:  "第12屆鐵人賽Day 4 Vue的單向資料流: 鬍子語法"
preview: ""
permalink: "/articles/2020-09-17"
date:   2020-09-17 09:56:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

昨天經過辛苦的環境安裝（擦汗），可以在Rails專案寫Vue了！先來個簡單的Vue起手式緩緩心情～  
在第2天我們有提到，[Vue.js 官網](https://012.vuejs.org/guide/) 這張MVVM架構的示意圖：
`Model`是資料邏輯：
`View`是使用者介面；
`VM`代表的是`View Model`。

<!-- more -->

![](https://i.imgur.com/JcXXD6y.png)  

我們可以看到原生`javascript`綁定事件的方式：

<iframe width="100%" height="300" src="//jsfiddle.net/tingtinghsu/jLmkebzo/14/embedded/js,html,result/dark/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

以及比較`Vue.js`的差別：
<iframe width="100%" height="300" src="//jsfiddle.net/tingtinghsu/41mrcLkz/6/embedded/js,html,result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

在Vue.js裡，是採用Vue instance（實例）作為原本的使用者介面(UI)及`Javascript`的資料處理邏輯的中間層，而非直接讓js去操作UI介面的元素。

這整塊是new出一個`Vue instance`，其最特別之處，
是讓`Model`邏輯的變化會同步到`View`介面上。
而`View`的資料變化也會同步到`Model`資料邏輯中、

(Model)
```
new Vue({
	el: "#app",
  data: {
  	message: "Vue feat. Rails"
  },
  methods: {
  	start(){
    	alert("鐵人賽開始!")
    }
  }
})
```

html (View)
```html
<!-- 比起原生js，Vue.js的寫法，會在view介面上，外層需要多一層div -->
<div id="app">
  <p id="title">{ { message } } </p>
  <button v-on:click="start">按我</button>
</div>
```

以上應該就可以很好的解釋，昨天的程式碼為何Vue js可以大大方方地在我的Rails專案首頁出現囉！

對照我的專案，前端model邏輯是這樣：

```
document.addEventListener('turbolinks:load', () => {
  new Vue({
    el: '#hello',
    data: () => {
      return {
        message: "第12屆鐵人賽專案，參賽確定！"
      }
    },
  })
})
```

而html (View)是這樣：

```html

<div id="hello">Vue.js x Rails { { message } } </div>

```

是不是有覺得Vue很好上手呢?!(~~鐵人賽才第四天，話不要說的太早~~)

# `{ { } } ` mustache: 鬍子語法

在昨天鐵人賽的`Vue instance`實例裡，我們有一個`data`屬性用來提供資料。
並且讓資料流向html (View)在標籤加上的`{ { message } }`鬍子語法內。
這被稱為`One-Way Data Flow`的`data binding`。（中文: 單向資料流的資料綁定）
```
document.addEventListener('turbolinks:load', () => {
  new Vue({
    el: '#hello',
    data: () => {
      return {
        message: "第12屆鐵人賽專案，參賽確定！"
      }
    },
  })
})
```

> Vue手冊：
The mustache tag will be replaced with the value of the message property on the corresponding data object. It will also be updated whenever the data object’s message property changes.  
無論何時，綁定的資料物件上 message property 發生了改變，插值處的內容都會更新。

## 單向資料流傳遞 - 鬍子語法還可以傳什麼?

鬍子語法也可以使用運算式（合法的表達式必須是one single expression）

```
{ { height / 100 } }

// 三元表達式OK!
{ { ok ? true : false } }


{ { message.split(',').join('') } }
```

有單一回傳值的運算式，就可以使用鬍子語法唷！

<iframe width="100%" height="300" src="//jsfiddle.net/tingtinghsu/2c0udyvj/5/embedded/js,html,result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

有單一回傳值的methods也可以放進去：

<iframe width="100%" height="300" src="//jsfiddle.net/tingtinghsu/xr39mjsg/7/embedded/js,html,result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>


# Vue Instance的物件屬性

## data可以放物件或function
```
  data: {
  	message: "Vue feat. Rails"
  }
```
昨天改寫`hello_vue.js`時，發現Vue生出來的檔案預設data寫法是JavascriptES6中箭頭函數的寫法。

```
  data: () => {
    return {
      message: "第12屆鐵人賽專案，參賽確定！"
  }
```

來做個統整一下，在今天鐵人賽的文章，我們已經練習了三種vue instance的屬性：`el`、`data`與`methods`!

| Vue instance的屬性 | 可以放置的資料型別 |
| -------- | -------- |
| el     | HTML元素、字串、function   | 
| data     |   物件、function   | 
| methods     |   function   | 


明天來了解Vue的另一個單向資料流語法`v-bind`吧！

Ref: 

* [Vue英文官網手冊: One-way Data Flow](https://vuejs.org/v2/guide/components-props.html#One-Way-Data-Flow)  
* [Vue中文官網手冊: 模板語法](https://cn.vuejs.org/v2/guide/syntax.html)  
* [VUE.JS 入門 Slide - Vue 2 新手必備的姿勢](https://docs.google.com/presentation/d/1RaXwt4n97OWUrMqel_-SKYTAldh9VhuaI0tbMJ31k0I/edit#slide=id.g58797a85ac_0_0)  