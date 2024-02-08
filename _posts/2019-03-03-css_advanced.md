---
title:  "css: 進階排版語法"
preview: "css advanced"
permalink: "/articles/2019-03-03-css_advanced"
date:   2019-03-03 10:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  

comments: true
---

CSS進階排版語法，包含解決`float`文繞圖與`clearfix`的問題，以及滑鼠移動效果、`box-shadow`的參數產生的陰影效果。

<!-- more -->

---
目錄:
* abstact
{:toc}

---



# float 文繞圖與 clearfix

## float的問題
```css
article {
  background-color: lightgray;
  padding: 10px;
}

img {
  width: 500px;
  float: left;
}
```
float可能會會產生`img`的高度超過`article`高度，導致文字框高度消失。

## 解法一：`clear: both`
在html用`style="clear: both"`清除圖片左漂浮、右漂浮的效果。

```htmlembedded=
<div style="clear: both">  </div>
```

## 解法二: `clearfix`

在html `div`標籤產生一個新的class。讓裡面的元素把高度撐開。

html
```htmlmixed=
<div class="clearfix">  </div>
```

css
```css
.clearfix {
 clear: both; 
}
```


# Width vs Max-Width

設定最大寬度或最小寬度，讓欄位大小根據瀏覽器視窗縮放時做調整。

html
```htmlmixed

    <div class="div1">Width=800</div>
    <div class="div2">Max Width=800</div>
    <div class="div3">Min Width=800</div>
```

css
```css
    div {
      text-align: center;
      font-size: 2rem;
      background-color: aquamarine;
      margin: 3px;
      color: tomato;
    }

    /*寬度固定*/
        .div1 { width: 800px; }

        /*會跟著螢幕寬度縮小，最大800，適合RWD*/
        .div2 { max-width: 800px; } 

        /*會跟著螢幕寬度放大，最小800，適合RWD*/
        .div3 { min-width: 800px; } 
```

# hover: 滑鼠移動過去時出現效果

html
```htmlmixed
<a href="#" class="link1">預設連結樣式</a>
<a href="#" class="link2">沒有底線的連結</a>
<a href="#" class="link3">滑鼠移過去會改變顏色，消除底線</a> 
```

css
```css
a { font-size: 1rem; font-weight: 300; }
    .link2 { text-decoration: none; }
    .link3:hover { 
      color: tomato; 
      transition: color 3s; /*漸變顏色，3秒後變紅*/      
      text-decoration: none; 
    }
```

#  box-shadow: 陰影效果

html
```htmlmixed
    <div id="shadow">shadow</div>
```

css
```css
    div#shadow {
      width: 200px;
      height: 200px;
      color:whitesmoke;
      background-color:teal;
      font-size: 3rem;
      font-weight: 300;
      font-family: sans-serif;

      box-shadow: 
      /*參數: 右 下 模糊效果 顏色(可加不透明度) */      
      10px 2px 20px rgba(0, 0, 0, 0.5),
      /*另一組用逗點隔開: 左 上 模糊效果 顏色*/         
        -5px -5px 10px wheat;
    }
```
# css簡寫順序

1. 簡寫的順序：有的排序很重要(如margin)，有的排序不重要(如border)。 
2. 未指名的值會被設為預設值。

html
```htmlmixed
    <div id="margin">margin</div>
```

css
```css
    div#margin {
      width: 200px;
      height: 200px;
      color:whitesmoke;
      background-color:teal;
      font-size: 2rem;
      font-weight: 300;
      font-family: sans-serif;
      
      /*border可以調換順序*/
      border: 10px solid lightblue;
      /*border: lightblue 10px solid;*/
      border-color: green; /*放後面的設定會蓋掉前面*/
      margin: 1px 2px 2px 1px;
}
```

# 相關連結

[HackMD筆記](https://hackmd.io/BPP-sYxDSS-cgYyhb1iP8g?view)  
[CodePen練習](https://codepen.io/tingtinghsu/pen/MxeQwQ)  
[網頁作品](../public/note/cssdemo/04-advanced.html){:target"_blank"}

