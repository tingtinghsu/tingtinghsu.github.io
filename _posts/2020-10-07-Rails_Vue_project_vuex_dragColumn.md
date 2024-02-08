---
title:  "第12屆鐵人賽Day 24 Rails專案開發 - Vuex狀態管理(2) mapState、mapGetters、mapActions、mapMutations"
preview: ""
permalink: "/articles/2020-10-07"
date:   2020-10-03 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

昨天鐵人賽我們kanban專案裡，把`column`改寫為Vuex狀態管理並透過beforeMount的方式從後端得到資料渲染在網頁上！

看起來好像前進了一大步，但其實Vuex還有很多東西值得研究...（遠目）

例如：

1. 昨天有程式碼裡，眼尖的觀眾應該有發現程式碼截圖中的`mapGetters、mapActions`還沒有完整說明；

2. 雖然現在可以把Column跟所屬的Ticket資料渲染在頁面上，

但當我們需要更動column的時候（例如第21天介紹的column拖拉功能）如下圖gif顯示：
   
![](https://i.imgur.com/kdH0aff.gif)  
  
拖拉完後、新的位置結果也還無法正確存入database，reflash頁面後又回復原來的樣子，並且console會出現錯誤訊息：

> [Vue warn] Computed property "columns" was assigned to but it has no setter.

那麼，要如何正確地在Vuex store裡update column的狀態，並且讓Vue拿到資料呢？ 

為了解決以上問題～～今天要來介紹幾個可以從Vuex裡面把資料/方法提出來給Vue使用的`helper輔助函式`！


再次推薦[官網影片](https://vuex.vuejs.org/)，
![](https://i.imgur.com/XIixU4y.jpg)

影片裡：這張截圖幫助我清楚理解到Vuex的四個部分：

1. State: store裡的資料狀態。
2. Getter: 相當於vue中的computed屬性，透過Getters我們可以得到想要的值。
3. Actions: 發出Commits呼叫Mutation。
4. Mutations: 更改 State 的方法。


# 介紹：Vuex提供的4個Helper`輔助函式`

為了讓Vue能夠直接使用Vuex的資料或方法，這裡把4個helper的對應整理如下：

|  Vuex    |  Vue    | 
| ---- | ---- |
| State | mapState：可取得 state 裡的資料。 |
| Getters | mapGetters：可以把store的getter方法，map到Vue的Computed屬性。 |
| Actions | mapActions 可取得 actions 裡的方法。|
| Mutations | mapMutations 可取得 mutations 的方法。 |


## 1. mapState：可以取得 state 裡的資料

我們知道有了vuex，不必再煩惱各個元件間如何傳值，而可以很直接地就透過`$store`來獲取不同的資料
，但是如果`vue`一次就需要多個vuex中的data的時候....

以一本書`book`，有書名、作者、價錢為例，我們在Vue裡拿store資料時不需要像醬子寫三遍：

```
title() { return this.$store.state.title }
author(){ return this.$store.state.author }
price() { return this.$store.state.price }
```

所以如果Vuex裡有多個state
我們就可以像這樣子：
```
import {mapState} from 'vuex'
export default {
  name: 'book',
  computed: mapState(['title','author','price'])
}
```

helper讓以上原本三句落落長的code等同於
`mapState(['title','author','price'])`。


## 2. mapGetter: 把store的getter，map到Vue的Computed屬性

![](https://i.imgur.com/TP62VHs.png)

以上的範例，我們理解到昨天專案裡程式碼中的第一條黃色虛線`getters`部分：

1. 右側：Vuex的 `getters`，透過允許傳參數(state)的getters以得到我們想要的值。（相當於`vue`中的`computed屬性`）

2. 左側：引入Vuex store的Vue元件，`import { mapGetters } from 'vuex';`之後，利用`mapGetters`，把專案裡`column`的值用`map`方式get進來。

然後，還記得剛剛一開始的Vue的警告訊息嗎？

> [Vue warn] Computed property "columns" was assigned to but it has no setter.

Computed中其實分成兩部分：`getter取值`和`setter給值`。

在一般的情況下，computed （只有 getter）來更新資料，如果沒有`setter`，Computed僅能幫我們觀察data屬性（唯讀），而無法給值；

當computed的屬性要被設值時，就會觸發setter。（例如拖拉`column`）

因此我們在此範例無法直接使用`mapGetter`，而是需要在`computed`內再加一個`set(val)`的function，並且使用`this.$store.commit('UPDATE_COLUMNS', val)`，去透過Mutations更改值。

```
  computed: {
    columns: {
      get(){
        return this.$store.state.columns
      },

      set(val){
        this.$store.commit('UPDATE_COLUMNS', val)
      }
    }
  },
```

## 3. mapAction: 可以取得vuex actions裡的方法

我們把程式碼再度稍加修改，並且把拖拉Column的method`dragColumn`從Vue移到Vuex狀態管理：

在`import { mapActions } from 'vuex';`之後，
以下程式碼中的黃色虛線部，代表透過`mapActions`這個helper把Vuex的`fetchColumn`(網頁一開始渲染column)和`dragColumn`(拖拉column)這兩個方法引入Vue。

![](https://i.imgur.com/vKET8mt.png)

並且在view進入點onlick綁定`dragColumn`事件

columns/index.html.erb
```
<div id="column" class="mt-2 px-3" data-kanbanid="<%= @kanban.id%>" >
  <!-- 綁定v-model: 拖完之後，資料也跟著改變位置 -->  
  <draggable v-model="columns" class="flex" @change="dragColumn">
    <Column v-for="column in columns" :column="column" :key="column.id"></Column>  
  </draggable>
</div>
```

## 4. mapMutation: 可以取得 mutations 的方法

雖然在本日鐵人賽還沒用到`mapMutation`，
不過還是來舉個例子介紹一下：

跟之前的`mapState`等語法類似，
Vue的`mapMutations`也是簡寫：

```
methods:{
 ...mapMutations(['GET_SALARY','GET_BONUS'])
}
```
等同於原本落落長的兩句：
```
getSalary(payLoad){
  this.$store.commit('GET_SALARY',payLoad)
}
getBonus(payLoad){
  this.$store.commit('GET_BONUS',payLoad)
}
```
`payLoad`參數可以在`調用`方法的時候時寫入。

```
<button @click="getSalary({ money: 40000 })">領薪水摟！</button>
<button @click="getBonus({ money: 60000 })">發年終獎金！</button>
```


Actions和Mutation的差別：
- 非同步方法(ajax、axios、setTimeout等) 要寫在Actions。
- mutations最重要的作用就是為了修改state，所以mutations只能放同步function。


呼～～Vuex狀態管理真是一門大學問！

最後來一張`dragColumn`method改成Vuex後，devtool中每一條mutation前後狀態的快照，做為今天的ending：）
![](https://i.imgur.com/HRKWEMN.gif)



Ref: 

* [Youtube: mapState, mapGetters, mapActions in Vuex](https://www.youtube.com/watch?v=ksDmbBqpw2A) 

* [vuex：弄懂mapState、mapGetters、mapMutations、mapActions](https://zhuanlan.zhihu.com/p/100941659)  

* [理解Vuex](https://mobilesite.github.io/2016/12/18/vuex-introduction/)  

* [Computed 中 getter 和 setter 觸發的時間點](https://pjchender.blogspot.com/2017/05/vue-computed-getter-setter.html)  