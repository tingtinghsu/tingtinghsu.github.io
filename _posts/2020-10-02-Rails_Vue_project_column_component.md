---
title:  "第12屆鐵人賽Day 19 Rails專案開發 - 建立Vue元件: Column component"
preview: ""
permalink: "/articles/2020-10-02"
date:   2020-09-30 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---

在昨天的鐵人賽，我們利用rails的`scaffold`指令，迅速地將看板`Kanban`的`CRUD`調整成符合專案適合的樣貌，並完成了使用者登入後頁面，列出了目前筆者在現實生活中、需要進行敏捷開發的兩個Rails專案的`kanban`。

<!-- more -->

![](https://i.imgur.com/z2srPz1.png)
(寫個專案來改善real-life situation會遇到的問題，改善自己的生活，才是最寶貴的經驗啊～～)  


那麼，點進`Kanban`裡之後，揪竟～～會發生什麼事呢？

# 本日開發目標

`Column`的業務邏輯如下：

- 使用者可以建立很多`Kanban`
- 每個看板`Kanban`可以建立很多`Column`
- 每個`Column`可以有很多張票`Ticket`。（明天實作）

並且將`Column`換成Vue元件！

# 首先，建立第三個Model: Column

```
# position欄位是為了之後的拖拉效果做準備
rails g scaffold Column name kanban:belongs_to position:integer
```

檢視一下model，確定`kanban`與`column`的關聯性：

`kanban.rb`

每個kanban有很多columns
根據`dependent`相依性，如果刪掉了kanban，裡面的column也就一起刪掉

```
class Kanban < ApplicationRecord
  has_many :columns, dependent: :destroy
  belongs_to :user
  validates :name, presence: true
end
```

`column.rb`
```
class Column < ApplicationRecord
  belongs_to :kanban
  validates :name, presence: true  
end
```


# 在rails console建立幾筆資料column資料

`rails db:migrate`將欄位具現化後，通常在Rails專案裡，當model增加越來越多、model的關聯相對複雜的情況，
我們為了要確定model的關係，會先利用console見幾筆假資料來輔助接下來網頁的UI設計以及功能製作。

還記得昨天用網頁直接UI建立了兩個`kanban`，

* 我們想要抓出`id=1`使用者（就是我自己啦～）：

```
> u1 = User.first

=> #<User id: 1, email: "秘密", created_at: "2020-10-01 01:43:03", updated_at: "2020-10-01 07:12:27", name: "Ting">
```

* 並找到這位使用者的第2個看板：

```
> k2 = u1.kanbans.find 2

=> #<Kanban:0x00007fa40f8e4df0
 id: 2,
 name: "Vue.js feat. Rails專案",
 description:
  "相對容易上手的前端框架Vue.js，要如何整合網站後端開發最迅速的框架Rails呢？讓我們用30天來探索及實作～來一場「VR」Vue.js feat. Ruby on Rails前端後端交響曲吧！",
```

* 而且要在裡面建立幾筆column：  

根據想致敬的trello`Software Development` template:

![](https://i.imgur.com/TP0g3qY.png) 

我建立的columns欄位名稱如下：

```
> k2.columns.create(name: "Backlog")
> k2.columns.create(name: "Sprint Backlog")
> k2.columns.create(name: "Working On")
> k2.columns.create(name: "Bugs")
> k2.columns.create(name: "Done")
> k2.columns.create(name: "Testing")

```

## 點擊dashboard的kanban，可導向該看板的column

我期待的是，點入登入後首頁的任一`kanban`，就可以導向該`kanban`下，看到該專案的開發流程樣貌。

如下所示，點擊連結後應該會導向
`kanbans/2/columns`
![](https://i.imgur.com/FLhVM0s.png)


## 設計column的routes
```
Rails.application.routes.draw do
  resources :kanbans do
    resources :columns, except: [:new, :edit] do
    end
  end

  devise_for :users
  root 'pages#index'  // 登入前的首頁
  get "dashboard", to: "pages#dashboard" // 登入後的dashboard
end
```



## MVC: 修改columns controller

由本專案架構下，預期的routes路徑推測
`kanbans/2/columns` （點擊2號kanban，列出所有column）

進入columns列表之前，我們要先準備好`@kanban`這個實體變數，之後才能取到kanban的id：

columns_controller.rb
```
  def index
    @kanban = Kanban.find(params[:kanban_id])    
    @columns = @kanban.columns.all
  end
```

準備好了路徑，我們該如何設計column這個元件呢？


# 製作Column元件 

## Step 0. 事前準備：利用 Vue.js Lifecycle的概念，決定使用哪個Lifecycle hook把資料抓回來

因為接下來我們需要請Vue.js在適當的時機點把目前Rails專案裡的資料庫把抓資料來並渲染在網頁上，
所以來觀察[第14天的鐵人賽](https://ithelp.ithome.com.tw/articles/10246033)曾經整理過的這張Lifecycle paragram：

Mounting: 掛載階段

![](https://i.imgur.com/nBoMs75.png)

| hooks | 用途 |
| -------- | -------- |
| beforeMount     |  在執行元素掛載，畫面渲染至瀏覽器前的階段，此時render method第一次被呼叫，可以讓我們預處理DOM。   |
| mounted     | 元素已掛載，`el`建立完成。     |


以本日的實作需求來說，我們希望在render前就能把資料取得，所以掛`Lifecycle hook`最佳的時機點是`beforeMount()`

## Step 1. 確定column元件的資料要從哪裡得到

使用scaffold時，rails已經幫我們長好了json檔，可以直接拿來使用

例如第2個`kanban`裡的`column`有這些:

![](https://i.imgur.com/9YnQP1l.png)

接下來就是重頭戲了！


## Step 2. 決定`Vue app`的掛載點

我們的要掛Vue.js的`Root`是`id="column"`的`div`

這個`div`也偷偷藏了`kanbanid`的`dataset`（剛剛column controller鋪好的梗，讓`@kanban`實體變數可以直接拿來用）
以利第三步用ajax打api到後端

接著當我們要到資料時，會用`v-for`把column一個一個渲染在頁面上。

`columns#index.html.erb`
```
<div id="column" class="mt-2 px-3 flex" data-kanbanid="<%= @kanban.id%>" >
  <!-- Column元件的位置 -->
  <!--  -->
  <Column v-for="column in columns" :column="column" :key="column.id"></Column>  
</div>
``` 

還記得`資料綁定v-bind`嗎?

`<Column v-for="column in columns" :column="column" :key="column.id"></Column>`
這句的意思是
是v-for迴圈的每個`column`值，透過`:column`傳給子元件


## Step 3. 設計column元件

在[第9天鐵人賽](https://ithelp.ithome.com.tw/articles/10242337)，曾經練習用`props`在Vue裡從父元件裡傳遞資料給子元件。

而在本專案我們的column元件是掛在`root`之下，因此會利用`props`傳進來column以供子元件使用。然後用css美化一下印出來的結果，看起來有一格一格欄位的感覺～

`components/kanban/column.vue`
```
<template>
  <div class="column">
    <!-- 使用props裡的column -->
    <div class="column-name">{{ column.name }}</div>
  </div>
</template>

<script>
  export default {              
    name: 'Column',
    // 父層餵資料給子元件    
    props: ["column"]
  }
</script>

<style lang="scss" scoped>
  .column{
    @apply .bg-teal-100 .mx-2 .w-64 .rounded;

    .column-name{
      @apply .px-3 .py-2 .font-thin;
    }
  }
</style>
```

## Step 4. 在`application.js`引入並註冊元件

要使用元件，必須要先引入及註冊
這點應該大家都不陌生了：
`import Column from "components/kanban/column"`
`components: { Column }`

application.js
```
require("@rails/ujs").start()
require("turbolinks").start()
require("@rails/activestorage").start()
require("channels")

const images = require.context('../images', true)
const imagePath = (name) => images(name, true)

import 'scripts'
import 'styles'

import Vue from "vue/dist/vue.esm";
import Column from "components/kanban/column"
import Rails from '@rails/ujs'

document.addEventListener("turbolinks:load", () => {
  let el = document.querySelector("#column");
  if (el){
    new Vue({
      el,
      data: {
        kanban_id: el.dataset.kanbanid,
        //columns陣列, 預設為空值
        columns: []
      },
      components: { Column },

      beforeMount(){
        // 打API
        Rails.ajax({
          url: `/kanbans/${this.kanban_id}/columns.json`,
          type: 'GET',
          dataType: 'json',
          success: result => {
            // 把ajax回傳結果放進columns陣列
            this.columns = result;
          },
          error: error => {
            console.log(error);            
          }
        });
      }
    });
  }
})
```

而本部分最重要的地方就是在
```
beforeMount(){
  });
```
裡面打ajax，用`GET`的動詞跟資料庫取得資料。

而在做這一步前，記得在開頭要引入`ujs`

`import Rails from '@rails/ujs'`
這可以讓我們可以在不更動原有的controller的情況下，就能用Rails的慣例處理route與action。



完成圖：

![](https://i.imgur.com/c6bZYD9.png)

Column的元件搞定啦～～


這篇鐵人賽的文，從做專案到寫文章研究了大概五到六個小時...（擦汗）
但第一次成功地在專案使用Vue.js生命週期的概念，很有收穫～

明天要來寫Ticket的元件！


