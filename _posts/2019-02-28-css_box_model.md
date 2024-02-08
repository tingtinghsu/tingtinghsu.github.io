---
title:  "css: Box Model"
preview: "css box model"
permalink: "/articles/2019-02-28-css_box_model"
date:   2019-02-28 10:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  

comments: true
---
CSS的Box Model重點包含`margin`外邊界，`border`編框，和`padding`內邊界。
<!-- more -->

---
目錄:
* abstact
{:toc}

---

# 1. margin 外邊界

## 外邊界-上.右.下.左.語法

```css
p {
    margin-top: 10px;
    margin-right: 10px;    
    margin-bottom: 10px;
    margin-left: 10px;

}
```

## 外邊界語法簡寫
```css
p {
    margin: 10px 10px 10px 10px; /*上下左右外邊界皆10px*/
    margin: 10px;               /*數值若相同，可只寫一個*/
    margin: 50px 30px 50px 30px;  /*上下50px,右左30px*/ 
    margin: 50px 30px;            /*上下相同,右左相同*/ 
}
```



# 2. border 邊框

```css
p {
    border-width: 1px;   /*邊框寬度*/
    border-color: red;   /*邊框顏色*/

    border-style: solid;  /*邊框樣式: 實線*/
    border-style: dotted; /*邊框樣式: 點狀*/
    border-style: dashed; /*邊框樣式: 虛線*/

    border: 1px red solid; /*寬度.顏色.樣式簡寫成一行*/
}
```
# 3. padding 內邊界

```css
p {

    padding: 20px;
}
```

# 語法練習: margin, border, padding

html

```htmlmixed=
<h1>Box Model</h1>

<article>
  <h2>重點</h2>
  <p id="title">領導者的特質</p>
  <p id="zh">「我最常做的事情是，學會給予夥伴所需要的空間，並且信任我的團隊。」</p>
  <p id="en">Put them first. It’s a people-first mindset that unlocks sales success. Some people think of “sharks” as ruthless predators, but that’s not how I do business. I know how to innovate, disrupt the marketplace, orchestrate a killer investment deal, and grow a business. But my people-first perspective is at the heart of everything I do. I intentionally seek win-win relationships and solutions.
</p>
</article>

<footer>
  <p>&copy; 版權所有，翻譯不究</p>
</footer>
```
css:

```css
p#en {
  color: rgba(30,30,20,50);
  background-color: yellow;
  font-family: 'Courier New', Courier, monospace;
  width: 400px;

  margin-top: 50px;
  margin-left: 130px;
  margin-bottom: 50px;
  
  border: 2px brown dashed; /*寬度.顏色.樣式簡寫*/
  padding: 20px; /*內邊界*/
}
```
# html + css 顯示範例

<article>
  <h2>文章</h2>
  <p id="title">領導者的特質</p>
  <p id="zh">「我最常做的事情是，學會給予夥伴所需要的空間，並且信任我的團隊。」</p>
  <p id="en">Put them first. It’s a people-first mindset that unlocks sales success. Some people think of “sharks” as ruthless predators, but that’s not how I do business. I know how to innovate, disrupt the marketplace, orchestrate a killer investment deal, and grow a business. But my people-first perspective is at the heart of everything I do. I intentionally seek win-win relationships and solutions.
</p>
</article>
<style>
p#en {
  color: rgba(30,30,20,50);
  background-color: yellow;
  font-family: 'Courier New', Courier, monospace;
  width: 400px;
  margin: 40px 60px; 
  border: 2px brown dashed; /*以上三行簡寫*/
  padding: 20px;
}
</style>

<footer>
  <p>&copy; 版權所有，翻譯不究</p>
</footer>

# 4. 進階排版細節

## 4.1 內邊界`padding`和`box-sizing`

排版時，內邊界`padding`的排版位置會受到外邊界`border`和邊框寬度`border-width`影響。
解決此問題，可使用在需要排成同大小的文字框內的css加上
`box-sizing: border-box;`
(讓兩個文字框的寬度相同，變成包含`邊框寬度`的`含稅價`)

```css
.p1 {
  width: 400px;
  border: 20px brown dotted; /*以上三行簡寫*/
  padding: 20px;
  box-sizing: border-box;
}

.p2 {
  width: 400px;
  /*width: 480px; /*寬度要改成400+80,黃色部份才會一樣寬*/
  box-sizing: border-box; /*包含box邊框的寬度*/
}
```
## 4.2 邊框曲度 `border-radius`

### 4.2.1 圓角

```css
p {
   border-radius: 15px; 
}
```


 <p class="p1">Put them first. It’s a people-first mindset that unlocks sales success. Some people think of “sharks” as ruthless predators, but that’s not how I do business. I know how to innovate, disrupt the marketplace, orchestrate a killer investment deal, and grow a business. But my people-first perspective is at the heart of everything I do. I intentionally seek win-win relationships and solutions.
</p>

 <p class="p2">Put them first. It’s a people-first mindset that unlocks sales success. Some people think of “sharks” as ruthless predators, but that’s not how I do business. I know how to innovate, disrupt the marketplace, orchestrate a killer investment deal, and grow a business. But my people-first perspective is at the heart of everything I do. I intentionally seek win-win relationships and solutions.
</p>

<style>
.p1 {
  color: rgba(30,30,20,50);
  background-color: yellow;
  font-family: 'Courier New', Courier, monospace;
  width: 400px;
  border: 5px brown solid; /*以上三行簡寫*/
  border-radius: 15px 5px 10px 20px; /*左上.右上.左下.右下*/
  padding: 20px;
}

.p2 {
  color: rgba(30,30,20,50);
  background-color: yellow;
  font-family: 'Courier New', Courier, monospace;
  width: 400px;

}
</style>

### 4.2.2 圓

用css畫圖：
當`border-radius`超過長與高的一半，圖案會變成圓。
```css
.p {
  width: 100px;
  height: 100px;
  background-color: pink;
  border-radius: 90px;
}
```

<p class="round1"></p>
<style>
.round1 {
  width: 100px;
  height: 100px;
  background-color: pink;
  border-radius: 90px;
}
</style>

```css
.p {
  width: 200px;
  height: 200px;
  background-color: lightgreen;
  border-radius: 100px;
}
```

<p class="round2"></p>
<style>
.round2 {
  width: 200px;
  height: 200px;
  background-color: lightgreen;
  border-radius: 100px;
}
</style>

## 4.3 block區塊 與 inline

並不是所有元素都能調寬度和高度。來比較一下`block`與`inline`：

### 4.3.1 block 元素

block會把其他元素擠到下一行。

```htmlmixed=
<div> div 會占滿整個區塊，除非設定 width 寬度 </div>
```
<div class="block1"></div>
<div class="block2"></div>
<style>
.block1 {
  height: 100px;
  background-color: pink;
}  
.block2 {
  height: 100px;
  background-color: purple;
}
</style>

### 4.3.2 inline 元素

inline`不會`把其他元素擠到下一行，而且無法設定`width`和`height`。

html
```htmlmixed
<a href="#" class="link1">首頁</a>
```
css

```css
.link1 {
    background-color: yellow;
}
```
<div>
<a href="#" class="link1">首頁</a>
<a href="#" class="link1">簡介</a>
<a href="#" class="link1">作品</a>
<a href="#" class="link1">留言</a>
<style>
.link1 {
    background-color: yellow;
}
</style>
</div>


### inline設定寬度

加上`display: block;`，可將inline變成具有block元素的屬性去調寬度與高度。

```css
.link2 {
    background-color: yellow;
    display: block; /*重要，加上後可設定寬度高度邊界*/
}
```

<div>
<a href="#" class="link1">首頁</a>
<a href="#" class="link2">簡介</a>
<a href="#" class="link2">作品</a>
<a href="#" class="link2">留言</a>
<style>
.link2 {
    background-color: yellow;
    display: block; /*重要，加上後可設定寬度高度邊界*/
    padding: 10px;
    margin-bottom: 10px; /*跟下一個block隔開間隔*/    
}
</style>
</div>

### inline維持一排的型態

加上`display: inline-block;`

```css
.link3 {
    background-color: yellow;
    display: inline-block; /*維持一排的型態，可設定高度*/
    height: 10px;
    padding: 10px;
}
```

<div>
<a href="#" class="link1">首頁</a>
<a href="#" class="link3">簡介</a>
<a href="#" class="link3">作品</a>
<a href="#" class="link3">留言</a>
<style>
.link3 {
    background-color: yellow;
    display: inline-block; /*維持一排的型態，可設定高度*/
    height: 10px;
    padding: 10px;
}
</style>
</div>

# 5. Reset與Normalize

reset: 歸零

normalize: 不歸零

# 相關連結

[HackMD筆記](https://hackmd.io/szAI4CVzQsmOwouKfI5Jng?view)  
[CodePen練習](https://codepen.io/tingtinghsu/pen/WmQxvb)  
[網頁作品](../public/note/cssdemo/03-boxmodel.html){:target"_blank"}