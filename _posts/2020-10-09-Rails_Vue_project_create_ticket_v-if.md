---
title:  "第12屆鐵人賽Day 26 Rails專案開發 - 新增ticket（介紹v-if語法）"
preview: ""
permalink: "/articles/2020-10-09"
date:   2020-10-05 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---


昨天鐵人賽做完了`新增ticket`！所以現在專案裡，`Done`欄位完成了三張票～  

![](https://i.imgur.com/aFXFQCM.png)

不過，在繼續實作`編輯`及`刪除`ticket功能之前，  

我想要把輸入框先隱藏起來，等到使用者按`+`之後才出現～這樣的功能該如何實作呢？

# 語法介紹: 流程控制

今天想來介紹`v-if`和`v-show`，先來看簡單的範例：

## `v-if`

Eg. 去電影院時，身高小於120公分的小孩可以用半票價格，其他人都是全票價格

template
```html
<div id="app">
  <span v-if="height > 120">全票</span>
  <span v-else>半票</span>
</div>
```

js
```javascript
new Vue({
  el: "#app",
  data: {
    height: 100
  }
})
```

html
```
半票
```

## v-show

```html
<div id="app">
  <span v-show="height > 120">全票</span>  
</div>
```

如果不符合條件的話，html裡面什麼都不會顯示，但是按右鍵看網頁元素，原始的欄位透過`display: none`被隱藏了：

![](https://i.imgur.com/EPWh9Z4.png)

`v-show`的差別，在於`v-show`只是更改css，使用`<span style="display: none;"></span>`，只是讓判斷邏輯未通過的部分不在螢幕上顯示，因此效能較佳。`（v-show`跟`v-else`不能同時使用喔！）

參考Vue官網比較`v-if`與`v-show`的說明：

* v-if is “real” conditional rendering because it ensures that event listeners and child components inside the conditional block are properly destroyed and re-created during toggles. 
> v-if 是“真正”的條件渲染，因為它會確保在切換過程中條件塊內的事件監聽器和子組件適當地被銷毀和重建。

* v-if is also lazy: if the condition is false on initial render, it will not do anything - the conditional block won’t be rendered until the condition becomes true for the first time. 
> v-if也是惰性的：如果在初始渲染時條件為假，則什麼也不做——直到條件第一次變為真時，才會開始渲染條件塊。

* In comparison, v-show is much simpler - the element is always rendered regardless of initial condition, with CSS-based toggling.
> v-show就簡單得多——不管初始條件是什麼，元素總是會被渲染，並且只是簡單地基於CSS進行切換。

所以我們可以導出結論：

*如果資料更動不會太頻繁，而且當條件不符合時，想直接把整個資料從網頁上拿掉、不採用`display-none`的話，用`v-if`較好。*

# Rails專案: 修改UI邏輯

我想多加幾個`v-if`的邏輯判斷，修改一下UI: 

- `if` 使用者按下`+`按鈕時才出現`新增ticket`的輸入框，同時長出`取消`按鈕
- `if` 使用者按下`取消`按鈕，回復成原來的樣子

起手式：

- 把`+`按鈕和`data`綁定
- `data`再加一欄`屬性`：`addTicket: false`


為了幫助思考，畫面設計如下：
![](https://i.imgur.com/qEqoFTX.png)


接著思考步驟：

1. if 不顯示輸入框(`!addTicket`)，代表還沒按下`新增ticket`按鈕的狀態。

2. 當按下`新增ticket`按鈕(`@click="addTicket = true"`)時，狀態改為顯示輸入框(`addTicket= true`)。按鈕變成`建立`按鈕及`取消`按鈕

3. 當按下`建立ticket`按鈕`@click="createCard"`時，把輸入框的資料打到後端  

4. 當按下`取消`按鈕`@click="addTicket = false"`時，回到1.

撰寫邏輯：

html

```html
<div class="ticket-input">
  <button v-if="!addTicket" class="create-ticket-btn" @click="addTicket=true"><i class="fas fa-plus text-teal-100"></i></button> 

  <input v-if="addTicket" class="ticket-name" v-model="ticketname"></input>        
  <button v-if="addTicket" class="create-ticket-btn" @click="createTicket">建立</button>
  <button v-if="addTicket" class="cancel-ticket-btn" @click="addTicket=false">取消</button>
</div>
```

column.vue 元件
```
data: function () {
  return {
    tickets: this.column.tickets,   
    ticketname: '',
    addTicket: false
  }
}
methods: {
  createTicket(evt){
    evt.preventDefault();
    console.log(this.ticketname)
    let data = new FormData();
    data.append("ticket[column_id]", this.column.id);
    data.append("ticket[name]", this.ticketname);
    Rails.ajax({
    // 略
      });
      // 新增ticket完畢後，把`addTicket`的開關，關掉
      this.addTicket = false
  },
```


`新增ticket`的流程控制，設計完成！
![](https://i.imgur.com/GE8VWy6.gif)

Ref: 

 * [Vue官網：v-if](https://vuejs.org/v2/api/#v-if)  

 * [Vue官網：v-show](https://vuejs.org/v2/guide/conditional.html#v-show)