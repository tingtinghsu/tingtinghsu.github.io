---
title:  "第12屆鐵人賽Day 6 Vue instance的computed屬性"
preview: ""
permalink: "/articles/2020-09-19"
date:   2020-09-17 09:56:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

昨天我們討論了雙向事件處理: `v-model` 以及 `v-on`，讓我們可以跟使用者輸入的資料互動。
（例如對使用者的輸入的暱稱打招呼～）

如果要把使用者的輸入資料做一些`加工`，再顯示出來，就需要透過`v-on`結合`methods`的方式。

<!-- more -->

# 從v-model + v-on 結合 methods談起

例如：我們想要按下一個神奇的按鈕，就可以把錢包裡的錢多5倍！

這個神奇按鈕長這樣(使用`v-on`，語法糖衣簡寫為`@`)：

```html
  <button class="button is-primary is-light" @click="timesFive">乘以5倍</button>
```

寫一個`methods`:

```javascript
new Vue({
el: "#app",
  data: {
    show_me_yourmoney: "錢包裡有多少錢？",
    yourmoney: ""
  },
  methods: {
    greeting() {
      return "這是鐵人賽第六天！"
    },
    timesFive(){
    	this.yourmoney =  this.yourmoney * 5
    }
  }
})

```

這個五倍方法會和`v-model`綁住的資料連動，隨著使用者輸入的錢數做改變：
(v-bind的`:`也是語法糖衣)

```html
  <input class="input" type="text" :placeholder="show_me_yourmoney" v-model="yourmoney">
```

效果：我們希望按下一個按鈕，$$就可以多5倍！
（歡迎點選`Result分頁`玩玩看範例）

<iframe width="100%" height="300" src="//jsfiddle.net/tingtinghsu/vwcuysnk/41/embedded/js,html,result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>



# v-model + v-on 結合computed

`computed:{}`
假設有一個`tax()`函式會跟不同的變數值而連動:
為了讓methods裡的方法更好管理，
我們不要全部寫在`timesFive()`裡，

而是獨立在`computed`屬性裡，寫一個名字為`tax`的`method`。

所以我們就達到了這個目的：讓有跟tax有關聯的data的值發生改變的時候，computed也會重新計算。

```javascript
new Vue({
  el: "#app",
  data: {
    show_me_yourmoney: "錢包裡有多少錢？",
    yourmoney: "",
    taxrate: 0.05
  },
  methods: {
    greeting() {
      return "這是鐵人賽第六天！"
    },
    timesFive() {
      this.yourmoney = this.yourmoney * 5
    }
  },
  computed: {
    tax() {
      return Math.round(this.yourmoney * this.taxrate)*100/100;
    }
  }
})
```

效果：我們希望按下一個按鈕，$$就可以加薪5倍，而且自動算出要繳多少稅（逃～）
（歡迎點選`Result分頁`玩玩看範例）

<iframe width="100%" height="300" src="//jsfiddle.net/tingtinghsu/2kht10e3/30/embedded/js,html,result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

是不是很有趣呢？

至於data的`methods`和`computed`有什麼不一樣呢？[官網手冊](https://vuejs.org/v2/guide/computed.html)裡說到：

> *Instead of a computed property, we can define the same function as a method. For the end result, the two approaches are indeed exactly the same. However, the difference is that computed properties are cached based on their reactive dependencies. A computed property will only re-evaluate when some of its reactive dependencies have changed. This means as long as message has not changed, multiple access to the reversedMessage computed property will immediately return the previously computed result without having to run the function again.*  
翻譯蒟蒻：我們可以將同一函式定義為一個`method`而不是一個`computed`屬性。兩種方式的最終結果確實是完全相同的。然而，不同的是`computed`屬性是基於響應式依賴（ reactive dependencies ）進行緩存的。只在相關響應式依賴發生改變時才會重新求值。

所以差別就在於`cache`緩存了！在我們的範例裡，只要`tax()函式`裡所需的變數，其值還沒有改變，`computed`屬性會幫我們`立即地`回傳值，而不用像data裡的`methods`屬性一樣需要再執行一次函式。`computed`對於跑大量的計算很方便，但是如果你不希望有太多的cache的話，其實`methods`就很方便了。


# 什麼是響應式依賴（ reactive dependencies ）?

第一次聽到響應式(reactive)是因為知道`React`框架的命名就是從這個單字而來的。響應式程式語言（Reactive programming）如同Object-Oriented Programming（物件導向程式設計），是某一種程式典範(paradigm)，其最大的特色是：

**可以在程式語言中方便地表達靜態或動態的資料流。而相關的計算模型，會自動將變化的值，透過資料流進行傳播。**

所以我們才會在昨天及前天的鐵人賽介紹，強調Vue語法中`單向`或`雙向`的資料流概念。

至於什麼是響應式依賴（ reactive dependencies ）？以及Vue.js是如何做到響應式？嘛...這個議題就有點深入了。不過身為一個前端框架初心者，我還是想透過圖去學習一個知識。查了資料，對岸的一位前端工程師大大畫了這張Vue在`reactive`架構圖，表達出了當響應的資料變更時，如何通知它的使用方要做一些新的操作：

（裡面就有剛剛討論的`computed`唷！至於更深入的部分就要等功力高強的時候去讀Vue.js的原始碼了）
![](https://i.imgur.com/qamrdik.png)
[圖片source](https://zhuanlan.zhihu.com/p/85678790)


經過了三天的Vue語法暖身，發現前端框架的水越來越深了。明天我們要回去Rail專案裡活用Vue囉！

Ref: 

* [Vue英文官網手冊: Computed Caching vs Methods](https://vuejs.org/v2/guide/computed.html)  
* [Vue3 源碼解析系列 - 響應式原理（reactive 篇）](https://zhuanlan.zhihu.com/p/87899787)  
* [Vue3響應式系統源碼解析-單測篇](https://zhuanlan.zhihu.com/p/85678790) 
* [什麼是Reactive Streams（響應式流）?](https://matthung0807.blogspot.com/2020/03/reactive-streams.html) 
* [Wiki: 響應式程式設計](https://zh.wikipedia.org/wiki/%E5%93%8D%E5%BA%94%E5%BC%8F%E7%BC%96%E7%A8%8B) 