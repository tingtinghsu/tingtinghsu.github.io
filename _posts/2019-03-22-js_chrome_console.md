---
title:  "javascript 使用Chrome Console撰寫"
preview: "javascript OOP"
permalink: "/articles/2019-03-22-js_chrome_console"
date:   2019-03-22 09:56:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "javascript"  
comments: true
---


寫javascript時，我們常使用Google Chrome網頁瀏覽器進行測試。  
在Google Chrome網頁上按右鍵 -> 檢查，再點選`Console`即可。  
接下來我們要在Google Chrome上尋找prototype內的函式。一起來試試看吧！   
<!-- more -->

---
目錄:
* abstact
{:toc}

---

# collection

先設定一個陣列名為`list`

```javascript
>> var list = [1,2,3]
< undefined
/* 函數名稱 */
>> list
< Array(3) [1,2,3]

(3) […]

/*1. key: value*/
0: 1
1: 2
2: 3


length: 3

<prototype>: Array [] /* 原型是prototype */
```

1.`key[value]`

```javascript
list[0]
1
```

2.`.length`

```javascript
list.length
```

3.`.indexOf`函數

可以使用`list.indexOf`這個函數，  
是因為在`prototype`裡面有定義此函式。

```javascript
list.indexOf(1)
0
list.indexOf(2)
1
list.indexOf(3)
2
```

4.`prototype` (在其他的程式語言叫做`類別`)

只要是陣列，就可以共享prototype內的所有函式。

```javascript
<prototype>: []
​​
concat: function concat()
constructor: function Array()
copyWithin: function copyWithin()
entries: function entries()
every: function every()
fill: function fill()
filter: function filter()
find: function find()
findIndex: function findIndex()
flat: function flat()
flatMap: function flatMap()
forEach: function forEach()
includes: function includes()
indexOf: function indexOf()
join: function join()
keys: function keys()
lastIndexOf: function lastIndexOf()
length: 0
map: function map()
pop: function pop()
push: function push()
reduce: function reduce()
reduceRight: function reduceRight()
reverse: function reverse()
shift: function shift()
slice: slice()
length: 2
name: "slice"
...
...
...

<prototype>: function ()
```

# 自訂建構函式

我們還記得函數是可以重複利用的SOP，相同的功能寫成函數後就可以重複利用。接下來在Google Console裡寫一個函數試試：

```javascript
>> function Pokemon(name, skill) {
  this.name = name;
  this.skill = skill;
}
< undefined

>> Pokemon 
< function Pokemon()
V
< Pokemon()
​
arguments: null
caller: null
length: 2
name: "Pokemon"
​
prototype: Object { … }
​
<prototype>: function ()
```

# 自訂新物件

我們利用剛剛設計好的Pokemon函數來製造新的口袋怪獸:  

```javascript
>> var pikachu = new Pokemon('皮卡丘','電');
< undefined

pikachu
< Object { name: "皮卡丘", skill: "電" }
V
{…}
name: "皮卡丘"
skill: "電"
    <prototype>: Object { … }
    V
    constructor: function Pokemon() // 建構函式
        arguments: null
        caller: null
        length: 2
        name: "Pokemon"
        prototype: Object { … }
        V
        constructor: Pokemon()
        <prototype>: Object { … } // 源頭: 物件
        V
        __defineGetter__: function __defineGetter__()
        __defineSetter__: function __defineSetter__()
        __lookupGetter__: function __lookupGetter__()
        __lookupSetter__: function __lookupSetter__()
        constructor: function Object()
        hasOwnProperty: function hasOwnProperty()
        isPrototypeOf: function isPrototypeOf()
        propertyIsEnumerable: function propertyIsEnumerable()
        toLocaleString: function toLocaleString()
        toSource: function toSource()
        toString: function toString()
        valueOf: function valueOf()

```

在學習程式的過程中，把自己喜歡的情境帶入函數內思考，是不是很有趣呢?

# 相關連結

[HackMD筆記](https://hackmd.io/4xyXFCkQQnOFxcA3YjQ9DQ?view)  