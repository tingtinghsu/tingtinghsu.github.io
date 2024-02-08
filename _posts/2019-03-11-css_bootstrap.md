---
title:  "css: bootstrap"
preview: "css bootstrap"
permalink: "/articles/2019-03-11-css_bootstrap"
date:   2019-03-11 10:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  
comments: true
---

css: bootstrap  
`bootstrap`是基於CDN技術建構的而成，由twitter公司研發。讓我們無痛撰寫css，趕快一起來看看吧！
<!-- more -->

---
目錄:
* abstact
{:toc}

---


<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">

# 什麼是CDN?

CDN: Content Delivery Network

根據[Wiki](https://en.wikipedia.org/wiki/Content_delivery_network)介紹，CDN（內容分發網絡）是構建在網絡之上的內容分發網絡，依靠部署在各地的邊緣服務器，通過中心平台的負載均衡、內容分發、調度等功能模塊，使用戶就近獲取所需內容，降低網絡擁塞，提高用戶訪問響應速度和命中率。CDN的關鍵技術主要有內容存儲和分發技術。

```html
A content delivery network or content distribution network (CDN) is a geographically distributed network of proxy servers and their data centers.
```
  
# 使用 bootstrap

使用時將[bootstrap網址](https://getbootstrap.com/docs/4.1/getting-started/introduction/)內的程式碼引入`css`檔案內。
  
  
## 1. 按鈕 button

顏色 `btn-primary`
大小 `btn-lg`


元素可改顏色，primary為藍色
https://getbootstrap.com/docs/4.1/utilities/colors/

```css
.text-primary
.text-secondary
.text-success
.text-danger
.text-warning
.text-info
```

<p class="text-primary">.text-primary</p>
<p class="text-secondary">.text-secondary</p>
<p class="text-success">.text-success</p>
<p class="text-danger">.text-danger</p>
<p class="text-warning">.text-warning</p>
<p class="text-info">.text-info</p>

原始

```html
  <a href="#">回首頁</a>
  <a href="#">放到購物車</a>
```

  <a href="#">回首頁</a>
  <a href="#">放到購物車</a>

使用bootstrap

```html
  <a href="#" class="btn btn-success">回首頁</a>
  <a href="#" class="btn btn-danger btn-lg">放到購物車</a>
```

  <a href="#" class="btn btn-success">回首頁</a>
  <a href="#" class="btn btn-danger btn-lg">放到購物車</a>
  
  
## 2. 表格 table

`table-hover` 滑鼠移過時閃動
`table-striped` 將列上色

原始

```html
  <table>
    <thead>
      <tr>
        <td>品項</td>
        <td>頁數</td>
        <td>售價</td>
      </tr>
    </thead>  
    <tbody>
      <tr>
        <td>七龍珠</td>
        <td>100 頁</td>
        <td>80 元</td>
      </tr>
   <tbody>
  </table>
```  

使用bootstrap

```html
  <table class="table table-striped">
    <thead>
      <tr>
        <td>品項</td>
        <td>頁數</td>
        <td>售價</td>
      </tr>
    </thead>  
    <tbody>
      <tr>
        <td>七龍珠</td>
        <td>100 頁</td>
        <td>80 元</td>
      </tr>
   <tbody>
  </table>
```


# 3. 格線系統 grid system

bootstrap解說:  
https://getbootstrap.com/docs/4.1/layout/grid/  

`col-`+`數值`，(欄位的全部數值總和為12，依照比例分割)  

原始

```html
<article>
    <section class="content">內文</section>
    <aside class="promotion">廣告</aside>
</article>
```

使用bootstrap：隔線切出比例

```html

  <article class="row">
      <section class="content col-5">內文</section>
      <aside class="news col-4">消息</aside>
      <aside class="promotion col-3">廣告</aside>
  </article>
```

# 相關連結

[HackMD筆記](https://hackmd.io/GBDofaDJQI2FletOY6oJtQ?view)  
[CodePen練習](https://codepen.io/tingtinghsu/full/OqvNpL)  
[網頁作品](../public/note/cssdemo/10-bootstrap.html){:target"_blank"}