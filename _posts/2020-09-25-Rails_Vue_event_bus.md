---
title:  "第12屆鐵人賽Day 12 跨多層的Vue元件資料傳遞: event bus"
preview: ""
permalink: "/articles/2020-09-25"
date:   2020-09-22 09:58:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

這週都在討論`props`（從外層傳到內層元件）與`emit`（從內層傳到外層），
如果是平行關係的元件（而非父子關係），要如何進行資料傳遞呢?

這時候就會出動一台公車，叫做`event bus`啦！

首先請熟記：  
`$bus.$emit`: 發送事件  
`$bus.$on`: 接收事件

在今天的例子裡，從Vue Devtools看到元件的階層如下：
```
<Root>
  <Sender>
  <Receiver>
  <Foot>
```

我們來引用經典的日本電影《情書》(1995)，模仿一個`Sender`元件傳送資料給`Receiver`元件。

實作需求：

**Sender `$bus.$emit`: 發送事件**  

`this.$bus.$emit`發送`loveLetter`事件，透過JavaScript的`setTimeOut`非同步callback，3秒鐘後收到alert  

![](https://i.imgur.com/HhWpxib.png)

**Receiver `$bus.$on`: 接收事件**  
  
alert按下確定後，Receiver可以收到eventBus傳來新的message  

![](https://i.imgur.com/isDWek2.png)


步驟：

## Step1. Vue.js進入點：home.js設定全域的event bus

`let eventBus = new Vue({});`定義一個空的Vue instance

利用`Object.defineProperty()` 實現雙向繫結，以觀察`observe`屬性的值的變動

Object.defineProperty() 會帶3個引數:  
  
1.目標物件 `Vue.prototype`  
2.需要定義的屬性或方法的名字，在這裡是 `$bus`  
3.目標屬性所擁有的特性: 在這裡是 `get()`取值  

```
import TurbolinksAdapter from 'vue-turbolinks'
import Vue from 'vue/dist/vue.esm'
import Foot from "../components/foot"
import Sender from "../components/sender"
import Receiver from "../components/receiver"

Vue.use(TurbolinksAdapter)

document.addEventListener('turbolinks:load', () => {
  let el = document.querySelector("#content");

  if (el){

    Object.defineProperty(Vue.prototype, '$bus', {
      get() {
        return this.$root.bus;
      }
    });
    // 名為eventBus的vue instance
    let eventBus = new Vue({});
    new Vue({
      el,
      data: {
        day: "第 12 天",
        topic: "跨多層的Vue元件資料傳遞: event bus",
        bus: eventBus
      },
      components: { Sender, Receiver, Foot  }
    })    
  }
})
```

## Step2. `Sender`元件  

在created()這個hook上  
`this.$bus.$emit`發送`loveLetter`事件，透過JavaScript的`setTimeOut`非同步callback，讓3秒鐘後`Sender`元件的message被更換為電影情書的經典台詞。
```
<template>
  <div class="sender rounded-lg">{{ msg }}</div>
</template>

<script>
  export default {              
    data: function () {
      return {
        msg: '寄給你一封情書'
      }
    },
    created() {
      // async
      setTimeout(() => {
        // 發送事件
        this.$bus.$emit('loveLetter', {
          msg: 'Ting is reading: Love Letter／ラブレター',
          alert: '請收下'
        });

        this.msg = '九月的陽光下，我正偷看你的側臉。你的身影猶如蕩漾在春風中的一首歌，你一定全都知道，你一定全都不在乎，就這樣回過頭，清涼的一笑。'
      }, 3000);
    }
  }
</script>

<style scoped>
<!-- 略 -->
</style>
```

## Step3. `Receiver`元件

3秒鐘後`this.$bus.$on`會接收事件（可想成是搭上`on`eventBus這班名為`loveLetter`的事件公車）：
- 透過`event`更換msg。
- 把`event`裡的`alert`的值`請收下`透過彈出對話框顯示。

```
<template>
  <div class="receiver rounded-lg"><i class="far fa-heart mr-2"></i>{{ msg }}</div>
</template>

<script>
  export default {              
    name: '',
    data: function () {
      return {
        msg: '收件人：Ting'
      }
    },
    created() {
    // 註冊監聽事件
    this.$bus.$on('loveLetter', (event) => {
      this.msg = event.msg;
      alert(event.alert);
      console.log(event);
      // {msg: "Ting is reading: Love Letter／ラブレター", alert: "請收下"}
    });
  },
  }
</script>

<style scoped>
<!-- 略 -->
</style>
```

完成圖：
![](https://i.imgur.com/vFYeBwv.gif)


全域`event bus`雖然方便，但是不容易偵錯及維護，

所以請注意：  

1. 盡量在`同階層`的或是`跨越多階層`的元件溝通再使用。
父子元件溝通請使用前幾天聊的`props/emit`。
2. 大型的專案就要使用`Vuex`來管理狀態囉，

明天來介紹`Vuex`吧～之後的`Rails`專案應該會用到！

後記：`event bus`是我一直放在心上，但是之前還沒有花時間去弄懂的例子，終於趁著鐵人賽的絕佳機會，總算實作出一個小東西了～



Ref: 

* [用 event bus 跨多層元件的資訊傳遞](https://myiaj.github.io/2020/03/17/vue-event-bus/)  
* [[Vue] 跟著 Vue 闖蕩前端世界 - 07 組件溝通 event bus](https://dotblogs.com.tw/wasichris/2017/03/05/181549)  
* [[Vue.js] 跨元件的資料傳遞 Eventbus 簡易使用範例](https://chenuin.blogspot.com/2019/12/vuejs-eventbus.html)  
* [Use a Global Event Bus](https://laracasts.com/discuss/channels/vue/use-a-global-event-bus)  
* [JS Object.defineProperty(): (7) Vue.js 資料繫結](https://ithelp.ithome.com.tw/articles/10204588)  