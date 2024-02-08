---
title:  "第12屆鐵人賽Day 21 Rails專案開發 - Vue draggable套件拖拉column"
preview: ""
permalink: "/articles/2020-10-04"
date:   2020-10-03 10:00:00
layout: post
tags: 
  - "rail"
  - "vue"    
---


隨著鐵人賽過了2/3來到尾聲，我們的Kanban開始有了雛形。

而今天實作的目的，是要讓Kanban裡的欄位可以拖拉，而且能將拖拉完的結果存入資料庫！

![](https://i.imgur.com/syN8XfL.png)  

想到系統的拖拉功能，db裡面的資料表就要有定位（存放原始的位置以及拖拉過後的位置）。

# Rails後端: Acts as list

[Acts as list](https://github.com/brendon/acts_as_list)是可以讓我們在model裡透過`position`這個欄位在db裡找到對應的資料的Rails套件。

安裝好`gem`、`bundle install`之後，點進GitHub連結手冊一邊參考，一邊來更改我們的 model。這裡我們將position依據排序遞增

`kanban.rb`

```
class Kanban < ApplicationRecord
  has_many :columns, -> { order(position: :asc) }, dependent: :destroy
  belongs_to :user
  validates :name, presence: true
end
```

`column.rb`需要透過給定scope，作為排序的分組：

（例如在我的model裡，同一個`kanban`裡，每個`column`position的值是unique的！不然會排序大亂啊～）

`column.rb`

```
class Column < ApplicationRecord
  acts_as_list scope: :kanban  

  has_many :tickets, -> { order(position: :asc)}, dependent: :destroy
  belongs_to :kanban
  validates :name, presence: true  
end
```

後端的事前準備完成，接著來看前端：

#  Vue前端: draggable套件

寫javascript的朋友可能都聽過`Sortable.js`這個拖放排序列表、支援三大前端框架(angular、vue、react)的js外掛程式，而`Vue Draggable`就是基於`Sortable.js`而產生的套件。研究了Vue邁向20天，今天就來研究我的第一個Vue的第三方套件`Vue Draggable`。

首先透過`yarn`安裝Vue.Draggable 套件:

```
yarn add vuedraggable
```

接著一步一步實現這個功能：

# Step 0. 引入`draggable`套件並註冊元件

元件就像積木的概念一樣，網路上已經有很多大神利用Vue實作各種UI（如：圖表、日曆、編輯器等等）Component都寫好，當我們要使用時只需要引入套件並註冊元件就可以完成，而拖拉效果的`draggable`也是這樣使用：

application.js
```javascript
import draggable from 'vuedraggable';

document.addEventListener("turbolinks:load", function(event){
  let el = document.querySelector('#show-list');

  if (el) {
    new Vue({
      el,
      data: {
        kanban_id: el.dataset.kanbanid,
        columns: []
      },
      components: { List, draggable }
    });
  }
})
```

## Step 1. 在要拖拉的column加入`draggable components`並綁定v-model

view:
columns#index.html.erb

原本長這樣：
```
<div id="column" class="mt-2 px-3 flex" data-kanbanid="<%= @kanban.id%>" >
  <Column v-for="column in columns" :column="column" :key="column.id"></Column>  
</div>
```

用Draggable元件把`column`包起來

![](https://i.imgur.com/a8JpXn8.png)

```htmlmixed
<div id="column" class="mt-2 px-3" data-kanbanid="<%= @kanban.id%>" >
  <!-- 綁定v-model: 拖完之後，資料也跟著改變位置 -->  
  <draggable v-model="columns" class="flex">
    <Column v-for="column in columns" :column="column" :key="column.id"></Column>  
  </draggable>
</div>
```

登登登～前端就馬上出現拖拉效果了！ 

![](https://i.imgur.com/b9hnF1g.gif)  

但是眼尖的客倌應該有發現，我在上圖的動畫最後幾秒refresh重整頁面，`column`還是跑回第一列。
因此我們要寫一個js事件，監聽`column`移動、並紀錄`new position`，把最後會落腳到的位置用ajax往後端打。

## Step 2. 監聽`@change`事件，寫一個callback function`dragColumn`更新column移動位置

我們需要進一步綁定v-model，因為拖拉後，資料也跟著改變位置

columns#index.html.erb
```htmlmixed
  <draggable v-model="columns" class="flex" @change="dragColumn">
    <Column v-for="column in columns" :column="column" :key="column.id"></Column>  
  </draggable>
</div>
```

接著到掛載Vue app的地方寫一個method。目前這個method只是先用console.log印出來看看Vue Draggable有哪些參數可以運用

application.js

```javascript
document.addEventListener("turbolinks:load", () => {
  let el = document.querySelector("#column");
  if (el){
    new Vue({
      el,
      data: {
        kanban_id: el.dataset.kanbanid,
        columns: []
      },
      components: { Column, draggable },
      methods: {
        dragColumn(evt){
          console.log(evt)
        }
      },
      // 略
    });
  }
}
```

![](https://i.imgur.com/CJb1h8M.png)


以上可以看到console裡的`evt.moved`可以幫我們紀錄`column`
原本從陣列的第0個位置`oldIndex: 0`
移到第1個位置`newIndex: 1`
```
{moved: {…}}
moved:
element: {__ob__: Observer}
newIndex: 1
oldIndex: 0
__proto__: Object
__proto__: Object
```

為什麼我們可以透過Vue Draggable 拿到`newIndex`與`oldIndex`? 其實他是透過sortable js 的event object來取得HTML元素的相關位置，有興趣的捧油請[參考sortable js手冊](https://github.com/SortableJS/sortablejs/blob/master/README.md#event-object-demo)：

```
to: HTMLElement — list, in which moved element.
from: HTMLElement — previous list
item: HTMLElement — dragged element
clone: HTMLElement
oldIndex: Number| undefined — old index within parent
newIndex: Number| undefined — new index within parent
...
```

## Step 3. 設計routes路徑

我們接下來希望在看板2號裡面，將第1個column移到新的position(第2欄)，並且使用更新`PUT`的方式
往此路徑更新position： 

`/kanbans/2/columns/1/drag`

|  動詞   | 路徑 | Controller Path |
| --- | -------- | -------- |
|  PUT	   |    /kanbans/:kanban_id/columns/:id/drag(.:format)      |    columns#drag      |

因此目前路徑設計成如下圖所示：

routes.rb
```
  resources :kanbans do
    resources :columns, except: [:new, :edit] do
      member do
        put :drag
      end
    end
  end
```

## Step4 . MVC: ，controller新增action

接著要為這個`drag action`寫一個controller方法。
由[acts_as_list](https://github.com/brendon/acts_as_list#methods-that-change-position-and-reorder-list)查到語法，把資料插入在第n個position，我們可以使用`insert_at`

columns_controller
```
  def drag
    # byebug
    @column.insert_at(column_params[:position].to_i)
    # 打到後端，把移到新位置的資料render回前端
    # /kanbans/2/columns/2.json
    render 'show.json'
  end
```


## Step 5. `dragColumn`method: 移動完後，把API打到後端更新位置

FormData是[Web API: Form Data](https://developer.mozilla.org/zh-TW/docs/Web/API/FormData)提供表單格式，我們可以利用它將資料轉成表單的格式，並且以表單的形式回傳給後端。
以免重新整理頁面完，移動的資料就不見了！

application.js

```javascript
  methods: {
    dragColumn(evt){
      // new一個FormData()物件叫做data
      let data = new FormData();
      // acts as list 的 position：不是從0開始而是從1開始算，所以要新的位置要+1再append回去       
      data.append("column[position]", evt.moved.newIndex + 1)
      console.log(data)

      //  /kanbans/2/columns/1/drag(.:format)`
      Rails.ajax({
        url: `/kanbans/${this.kanban_id}/columns/${this.columns[evt.moved.newIndex].id}/drag`,
        type: 'PUT',
        // ES6語法：鍵與值名稱相同都是data，直接寫data即可
        data,
        dataType: 'json',
        success: result => {
          console.log(result);
        },
        error: error => {
          console.log(error);            
        }
      });
    }
  }
```

column拖拉功能完成，重新refresh頁面也OK！
![](https://i.imgur.com/foKYIPe.gif)

明天要進一步來拖拉ticket了～這樣就可以把本週的`ticket拖拉`這張票移動到`done`完成這個位置！

Ref: 

* [GitHub: Rails gem - Acts As List](https://github.com/brendon/acts_as_list)  

* [GitHub: Vue Draggable](https://sortablejs.github.io/Vue.Draggable/#/simple)  

* [Vue 使用 formData 方式向後台發送數據](https://segmentfault.com/a/1190000018859140) 

* [Web API: Form Data](https://developer.mozilla.org/zh-TW/docs/Web/API/FormData)

* [深入但不淺出，你不知道的 Vue v-model 黑魔法](https://medium.com/@milkmidi/%E6%B7%B1%E5%85%A5%E4%BD%86%E4%B8%8D%E6%B7%BA%E5%87%BA-%E4%BD%A0%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84-vue-v-model-%E9%BB%91%E9%AD%94%E6%B3%95-b43814a9c7ad) 