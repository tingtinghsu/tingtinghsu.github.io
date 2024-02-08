---
title:  "第12屆鐵人賽Day 14 Vue instance的生命週期"
preview: ""
permalink: "/articles/2020-09-27"
date:   2020-09-23 09:59:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

在[第12天鐵人賽](https://ithelp.ithome.com.tw/articles/10244568)前在利用`event bus`傳情書給某個元件的時候，  
可能眼尖的客倌會注意到，疑? 這個`created()`是不是還沒介紹到呢?

```
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
```

今天就要來講解Vue instance的生命週期掛鉤(Instance Lifecycle Hook)了！

官網很好心的提醒到～不用一開始就全部看懂`life cycle hook`所有的細節，通常是做到功能需要更熟悉原理的時候，再回來理解！
> You don’t need to fully understand everything going on right now, but as you learn and build more, it will be a useful reference. [Ref.](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram)


# 我們目前已經熟悉的背景知識 

一個Vue Instance會有許多個選項物件(Options)，  
當實體被創造出來時，需要傳入所有在這個Vue實體需要用到的屬性選項，例如`el`,`data`, `template`等，
目前鐵人賽過了快一半，已經使用到的屬性選項有這些：
```
new Vue({
  el: '', // Vue app的掛載點
  data: {

  },
  methods: {

  }
  computed: {

  },
  template: {

  },
  components: {

  },
  // 還有很多其他的屬性選項
  // 包括生命週期鉤子
  // life-cycle-hooks: {
  
  //}
})
```

其中，`life-cycle-hooks`像是像生物被創造出來一樣，有「出生、長大、死亡」類似的週期，

而我們可以在每個實體從初始化、到被創建、被銷毀的特定個階段做一些事情。

來一張官網經典的`Lifecycle Diagram`，矩形的部分是我們可以掛上額外需要的程式碼的時機點:

![](https://i.imgur.com/gjhQ5xm.png)


而我們可以把這些Vue instance的時機點分成四個階段：

## 1. Creation: 元件初始化階段  

![](https://i.imgur.com/bZbQJC2.png)

| hooks | 用途 |
| -------- | -------- |
| beforeCreate     |  instance初始化完成。但`data`還沒建立、`el`也還沒被建立。   |
| created     | instance建立完成，可以使用`data`以及其他選項物件的屬性。`el`還沒被建立。     |

這就是為什麼在前兩天的`event bus`例子時，`created()`可以使用`this.msg`的原因，因為除了`el`，其他選項物件都已經成為可以使用的狀態。

```
  export default {              
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
```


## 2. Mounting: 掛載階段  

![](https://i.imgur.com/nBoMs75.png)

| hooks | 用途 |
| -------- | -------- |
| beforeMount     |  在執行元素掛載，畫面渲染至瀏覽器前的階段，此時render method第一次被呼叫，可以讓我們預處理DOM。   |
| mounted     | 元素已掛載，`el`建立完成。     |

## 3. Updating: 資料更新階段，並重新渲染畫面

![](https://i.imgur.com/xWW9O35.png)

| hooks | 用途 |
| -------- | -------- |
| beforeUpdate     | `data`更新之前，Virtual DOM還不會被渲染。   |
| updated     | `data`更新完成，Virtaul DOM完成渲染。新的view顯示在畫面上。    |

## 4. Destruction: 元件銷毀階段

![](https://i.imgur.com/DcuXaQ6.png)

| hooks | 用途 |
| -------- | -------- |
| beforeDestroy     |  被銷毀之前，此時instance仍然還可以使用。   |
| destroyed     | instance已經被銷毀，綁定的事件被解除。     |

# 同場加映：Rails的The Object Life Cycle物件生命週期

看到Vue.js的`before created`想起了在Rails專案裡的物件生命週期也會經歷過幾個階段：

> During the normal operation of a Rails application, objects may be created, updated, and destroyed. Active Record provides hooks into this object life cycle so that you can control your application and its data.   
翻譯蒟蒻：在Rails應用程式的正常運行期間，可能會創建，更新和銷毀物件，所以Active Record提供了該物件生命週期的掛鉤，以便於我們控制應用程式及資料。

Active Record的 life cycle hooks：

```
## 建立物件：
before_validation
after_validation

before_save
around_save

before_create
around_create

after_create
after_save

after_commit/after_rollback

## 更新物件：
before_validation
after_validation

before_save
around_save

before_update
around_update

after_update
after_save
after_commit/after_rollback

## 刪除物件:：
before_destroy
around_destroy
after_destroy

after_commit/after_rollback

```


例如：我們在Rails的User Model裡，當建立一名user instance前，先利用`before_create` callback執行對其密碼進行加密的method。

```
class User < ApplicationRecord
  validates :account, presence: true, uniqueness: true
  validates :email, presence: true, uniqueness: true

  before_create :encrypt_password

  private
  def encrypt_password
    # 略
  end
end
```


是不是跟Vue的實體生命週期有異曲同工之妙呢?

然而和Rails不同的是，Rails的新增/更新/刪除物件是與資料庫內的資料表進行互動，

而Vue在前端處理上多了一個透過掛載`mounting`的hook處理`畫面`渲染至瀏覽器前的階段。

所以下一篇打算來舉個例子，實作在Vue instance裡使用axios來處理ajax，而這個部分就會在生命週期的`mounting`階段進行喔！

（明天就是第15天了呢！快要完成二分之一了拍拍手）

Ref: 

* [Vue官網: Lifecycle Diagram](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram)  

* [Vue 實體與生命週期](http://blog.tonycube.com/2017/04/vuejs-8-lifecycle.html)  

* [Vue Instance](https://cythilya.github.io/2017/04/11/vue-instance/)  

* [為你自己學Ruby on Rails - Model 驗證及回呼](https://railsbook.tw/chapters/19-model-validation-and-callback.html)  

* [Active Record Callbacks - The Object Life Cycle](https://guides.rubyonrails.org/active_record_callbacks.html#running-callbacks)  

* [Active Record 回呼](https://rails.ruby.tw/active_record_callbacks.html)  