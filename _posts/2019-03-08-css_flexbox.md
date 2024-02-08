---
title:  "css: flexbox排版"
preview: "css flexbox"
permalink: "/articles/2019-03-08-css_flexbox"
date:   2019-03-08 11:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  
comments: true
---

css: flexbox 進階排版  
`flexbox`幫助我們更輕鬆地設定`column`和`row`的排列方向和順序。
<!-- more -->

---
目錄:
* abstact
{:toc}

---

瀏覽器支援程度：使用can I use查詢  
https://caniuse.com/#search=flexible

flexbox各參數範例整理  
https://codepen.io/tingtinghsu/full/EMmNxK

首先，在`.container`裡加上`display: flex;`


```css
.container {
  background-color: #f1f1f1;
  border: 1px solid black;
  padding: 5px;
  max-width: 800px;
  height: 400px;
  display: flex; /*裡面的元素用flex呈現*/
}

div {
  text-align: center;
  height: 50px;
  background-color: lightblue;
  border: 1px solid gray;
}
```

# A. flex-direction 列.欄的朝向

flex-direction有4種參數

```css
.container {
    flex-direction: row;
    flex-direction: row-reverse;
    flex-direction: column;
    flex-direction: column-reverse;
}
```

## 記憶方法

方向有無reverse
決定數字列從左或右開始，欄從上或下開始

# B. justfiy-content 水平對齊位置

justfiy-content有6種參數:

```css
.container {
    justify-content: flex-start; /*靠著頭*/
    justify-content: flex-end;  /*靠著尾巴*/
    justify-content: center;   /*置中*/
    
    /*分散對齊，每個元素的左右都會均分留白*/ 
    justify-content: space-around; 
    /*分散對齊，最左最右不會被分到，要留白時用padding*/ 
    justify-content: space-between;
    /*分散對齊，均分留白為n+1份*/    
    justify-content: space-evenly;
}
```

## 記憶方法

列左右.欄上下

## 列

數字從左到右，列靠左（頭）

```css
.container {
    flex-direction: row;
    justify-content: flex-start;
}
```

數字從左到右，列靠右（尾）

```css
.container {
    flex-direction: row;
    justify-content: flex-end;
}
```

## 列reverse

fd有reverse，jc列就從靠著左右的`反方向`對齊

數字從右到左，列靠右（頭）

```css
.container {
    flex-direction: row-reverse;
    justify-content: flex-start;
}
```

數字從右到左，列靠左（尾）

```css
.container {
    flex-direction: row-reverse;
    justify-content: flex-end;
}
```

## 欄

數字從上到下，欄靠上（頭）

```css
.container {
    flex-direction: column;
    justify-content: flex-start;
}
```

數字從上到下，欄靠下（尾）

```css
.container {
    flex-direction: column;
    justify-content: flex-end;
}
```
## 欄reverse

fd有reverse，jc欄就從靠著上下的`反方向`對齊

數字從下到上，欄靠下（頭）
```css
.container {
    flex-direction: column-reverse;
    justify-content: flex-start;

}
```

數字從下到上，欄靠上（尾）

```css
.container {
    flex-direction: column-reverse;
    justify-content: flex-start;

}
```

# C. align-items 垂直對齊方式

align-items有4種參數:

```css
.container {
    align-items: flex-start; /*靠著上*/
    align-items: flex-end;  /*靠著下*/
    align-items: center; /*垂直置中*/

    /*若無設定高度，align-items預設stretch至元素高度。*/
    align-items: stretch;
}
```

## flex-start, flex-end

根據fd的row或colunm是正反排序.調整頭尾

```css
.container {
    align-items: flex-start; /*靠著上*/
    align-items: flex-end;  /*靠著下*/
}

```

## flex-center

水平垂直都置中，將ai和jc都設為center

```css
/**/
.container {
    align-items: center; /* y軸，垂直置中*/
    justify-content: center;  /* x軸，水平置中*/
}  
```

## 記憶方式

row: 左到右; row-reverse: 右到左。
column: 上到下; column-reverse: 下到上。

## align-items與列的4種排列組合

數字從左到右，列靠上（頭，左上）

```css
.container {
    flex-direction: row;
    align-items: flex-start;
}
```

數字從左到右，列靠下（尾，左下）

```css
.container {
    flex-direction: row;
    align-items: flex-end;
}
```

數字從右到左，列靠上（頭，右上）

```css
.container {
    flex-direction: row-reverse;
    align-items: flex-start;
}
```

數字從右到左，列靠下（尾，右下）

```css
.container {
    flex-direction: row-reverse;
    align-items: flex-start;
}
```

## align-items與欄的4種排列組合

數字從上到下，欄靠左（頭，左上）

```css
.container {
    flex-direction: column;
    align-items: flex-start;
}
```

數字從上到下，欄靠右（尾，右上）

```css
.container {
    flex-direction: column;
    align-items: flex-end;
}
```

數字從下到上，列靠下（頭，左下）

```css
.container {
    flex-direction: column-reverse;
    align-items: flex-start;
}  
```

數字從下到上，列靠下（尾，右下）

```css
.container {
    flex-direction: column-reverse;
    align-items: flex-end;
}  
```

# D. flex-wrap

flex-wrap有3種參數:

```css
.container {
  flex-wrap: nowrap; /*預設值*/
  flex-wrap: wrap; /*超過寬度的元素換到下一行*/
  flex-wrap: wrap-reverse; /*換行時元素排序反轉*/
}
```

## 1. nowrap

```css
.container {
  max-width: 800px;
  flex-wrap: nowrap; /*預設值*/
}  

div {
  width: 230px; /*230*5已經超過800px，預設no-wrap會fit container設定大小*/
}
```

## 2. wrap

當內容太多時，用wrap換到下一行

```css
.container {
  max-width: 800px;
  flex-wrap: wrap; /*其中兩個元素換到下一行*/
}  

div {
  width: 230px;
}
```

## 3. wrap-reverse

```css
.container {
  max-width: 800px;
  flex-wrap: wrap-reverse; /*換行時元素排序反轉*/
}  

div {
  width: 230px;
}
```

# E. align-content

align-content =
`align item(垂直對齊方式)` + `justify-content(水平對齊位置)`的組合，
常用來處理`多行內容之間`的垂直對齊，或分散對齊方向。

6種參數:

```css
.container {
    align-content: flex-start; /*靠著頭*/
    align-content: flex-end;  /*靠著尾巴*/
    align-content: center;   /*置中*/

    /*若無設定高度，align-content預設stretch至元素高度。*/
    align-content: stretch;
    /*分散對齊，每個元素的上下都會均分留白*/
    align-content: space-around;
    /*分散對齊，最上最下不會被分到，要留白時用padding*/
    align-content: space-between;
}
```

## 1. flex-start

```css
.container1 {
  align-items: center; /*垂直置中*/
  justify-content: center;  /*水平置中*/
  flex-wrap: wrap;
  align-content: flex-start; /*整段內容靠著頭(上)*/
}
```

## 2. flex-end

```css
.container2 {
  align-items: center; /*垂直置中*/
  justify-content: center;  /*水平置中*/
  flex-wrap: wrap;
  align-content: flex-end;  /*整段內容靠著尾（下）*/
}
```

## 3. center

```css
.container3 {
  align-items: center; /*垂直置中*/
  justify-content: center;  /*水平置中*/
  flex-wrap: wrap;
  align-content: center;  /*整段內容於y軸置中*/
}
```

## 4. stretch

```css
.container4 {
  align-items: center; /*垂直置中*/
  justify-content: center;  /*水平置中*/
  flex-wrap: wrap;
  align-content: stretch;  /*整段內容靠著尾（下）*/
}
```

## 5. space-around

```css
.container5 {
  align-items: center; /*垂直置中*/
  justify-content: center;  /*水平置中*/
  flex-wrap: wrap;
  align-content: space-around; /*均分整段內容的上下留白*/
}
```

## 6. space-between

```css
.container6 {
  align-items: center; /*垂直置中*/
  justify-content: center;  /*水平置中*/
  flex-wrap: wrap;
  align-content: space-between;  /*均分整段內容的上下留白，但不含最上與最下*/
}
```

# F. flex-grow / flex-shrink

## flex-grow: 空間太多的時候

讓其中1塊吃掉剩下的空間，其他預設為0

```css
.item1-3 {
  flex-grow: 1;
}
```

讓其中2塊平分剩下的空間，其他預設為0

```css
.item1-2 {
  flex-grow: 1;
}
.item1-3 {
  flex-grow: 1;
}
```

分配剩下的空間，第2塊2份，第3塊1份，其他預設為0

```css
.item1-2 {
  flex-grow: 2;
}
.item1-3 {
  flex-grow: 1;
}
```

## flex-shrink: 空間不夠的時候

```css
.container {
  background-color: #f1f1f1;
  border: 1px solid black;
  padding: 5px;
  max-width: 800px; /*整個寬度為800*/
  height: 400px;
  display: flex; /*裡面的元素用flex呈現*/
  flex-direction: row;
}
div {
  width: 250px; /*  width: 太寬了，換行成多行 */
  height: 100px;
  text-align: center;
  color: #FFF;
  background-color: lightblue;
  border: 1px solid gray;
}
```

讓其中1塊犧牲部分的空間

```css
.item1-3 {
  flex-shrink: 2;/*其他flex-shrink預設為 1;*/
}
```

# G. order 改變元素的順序

```css
.item1-3 {
  order: -1; /*預設值為0, -1為往前, 1為往後*/
  /*若其他的元素都0, .item 1-3 order: -1會在最前面*/
}

.item1-2 {
  order: 1; /*預設值為0, -1為往前, 1為往後*/
  /*若其他的元素都0, .item 1-2 order: 1會在最後面*/
}
```

# 相關連結

[HackMD筆記](https://hackmd.io/TsygxXfBR4i9PnV8aS5KvA?view)  
[CodePen練習](https://codepen.io/tingtinghsu/full/EMmNxK)  
[網頁作品](../public/note/cssdemo/08-flexbox.html){:target"_blank"}