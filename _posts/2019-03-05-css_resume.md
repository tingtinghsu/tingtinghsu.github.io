---
title:  "css: 個人簡歷"
preview: "css resume"
permalink: "/articles/2019-03-05-css_resume"
date:   2019-03-05 10:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  
comments: true
---

CSS: 個人簡歷  
本篇將使用CSS設計個人簡歷，我們會用到之前切版的技巧。
<!-- more -->

---
目錄:
* abstact
{:toc}

---

# 1. 切版

```html
<section class="info">
</section>
    
<section class="content">
    <section class="about">
    </section>
    <section class="contacts-and-skills">
        <section class="contacts">
        </section>
        <section class="skills">
        </section>
    </section>
</section>
```

# 2. VS Code快捷鍵

以css舉例:

```html
 <style>
  body {
    width: 100px; /*w100* + tab*/
    height: 100px;  /*h100* + tab*/
    background-color: #fff; /*bgc + tab*/
  }
  main {
     margin-left: auto; /*ml + tab*/
     margin-right: auto; /*mr + tab*/
  }
  </style>
```

# 3. 易忽略的重點整理

## 3.1 `clearbox`

讓flow起來的欄位高度全部被`clearbox`算進去

html

```html
    <div class="clearfix"></div>
/*置於頂端*/
```

css

```css
/*置於頂端*/
.clearfix {
  clear: both;
}
```

## 3.2 兩欄式加起來100%

```css
.info {
  width: 26%;
  margin-left: 2%;
  margin-right: 2%;  
  float: left;
}

.content {
  width: 70%;
  float: left;
}
```

## 3.3 邊框弧度 `border-radius`

只要上方有弧度，下方不要

```css
.bio {
  background-color:#a9ebdc;
  text-align: center;
  border-radius: 7px 7px 0 0; /*左上 右上 右下 左下*/
}
```

畫圓形時，弧度設為高與寬度的一半

```css
.bio img.avatar {
  width: 200px;
  height: 200px;
  border-radius: 100px;
  margin-top: 40px;
  margin-bottom: 10px;
}
```

## 3.4 外邊距 `margin`與內邊距 `padding`

```css
.social-links i {
  font-size: 1rem;
  margin: 5px; /*字元之外的間距*/
  padding-bottom: 5px; /*底部留白*/
}
```

## 3.5 下載按鈕

```css
.download {
  background-color: #FFF;
  text-align: center;
  padding: 20px;    /*按鈕的周圍留白*/
}

.download a {
  /*字體*/
  font-size: 1rem;
  font-weight: 400;
  color: #FFF;
  text-align: center;
  text-decoration: none;
  
  /*圖案*/
  display: block;
  background-color: #034F50;
  width: 120px;
  height: 20px;
  border-radius: 25px;
  margin: auto; /*按鈕置中*/
  padding: 10px; /*字元間距*/
  
}
```

## 3.6 字體變色

超連結內記得幫class命名。

html

```htmlmixed
      <div class="download">
        <a href="#" class="colorchange">下載履歷</a>
      </div>

```

css

```css
.colorchange:hover { 
  color: lemonchiffon; 
  transition: color 1s; /*漸變顏色，3秒後變紅*/      
  text-decoration: none; 
}
```

## 3.7 `box-sizing`,`box-shadow` 邊框效果

```css
.content {
  width: 70%;
  float: left;
  background-color: #fff;
  padding-left: 50px;
  padding-right: 50px;  

  box-sizing: border-box; /*讓左右兩欄相加100%*/
  border-radius: 10px; /*邊界圓角.陰影*/
  box-shadow: 2px 2px 4px rgba(0,0,0,0.2);
}
```

## 3.8 `line-height` 行高

```css
.about p {
  font-size: 0.8rem;
  line-height: 25px; /*行高*/
  color: #2d2a36;
  padding-bottom: 15px;
}

.contacts li, .skills li {
  line-height: 40px; /*調行距*/
  font-weight: 300; /*調粗細*/  
}
```

## 3.9 調`font-awesome`

可用` margin-right`讓右邊空間增大。

```css
.contacts i, .skills i { /*調font-awesome*/
  font-size: 1rem;
  color: gray;
  margin-right: 10px;
}
```


# 相關連結

[HackMD筆記](https://hackmd.io/Z4b6CJZUS4eiunXLrH60eg?view)  
[CodePen練習](https://codepen.io/tingtinghsu/full/rROzWq)  
[網頁作品](../public/note/cssdemo/06-resume.html){:target"_blank"}

# 完成品連結

# 附錄: 前端工具

* 引入的連結，可放在
    1. `<head></head>`標籤裡面。
    2.  css資料夾內。

* 自己寫的css放在最後，才不會被引入檔案的預設值蓋過。

## 1. 顏色

https://color.adobe.com/zh/create/color-wheel/

## 2. 字體 font-awesome

https://fontawesome.com/start

```htmlmixed
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.7.2/css/all.css" integrity="sha384-fnmOCqbTlWIlj8LyTjo7mOUStjsKC4pOpQbqyi7RrhN7udi9RwhKkMHpvLbHG9Sr" crossorigin="anonymous">

```

## 3. normalize.css

https://necolas.github.io/normalize.css/

```css
/*! normalize.css v8.0.1 | MIT License | github.com/necolas/normalize.css */

/* Document */
/* 1. Correct the line height in all browsers.
 * 2. Prevent adjustments of font size after orientation changes in iOS.*/

html {
  line-height: 1.15; /* 1 */
  -webkit-text-size-adjust: 100%; /* 2 */
}

/* Sections */
/* Remove the margin in all browsers.*/

body {
  margin: 0;
}

/* Render the `main` element consistently in IE.*/

main {
  display: block;
}

/* Correct the font size and margin on `h1` elements within `section` and * `article` contexts in Chrome, Firefox, and Safari.*/

h1 {
  font-size: 2em;
  margin: 0.67em 0;
}

/* Grouping content*/
/* 1. Add the correct box sizing in Firefox.
 * 2. Show the overflow in Edge and IE.*/

hr {
  box-sizing: content-box; /* 1 */
  height: 0; /* 1 */
  overflow: visible; /* 2 */
}

/* 1. Correct the inheritance and scaling of font size in all browsers.
 * 2. Correct the odd `em` font sizing in all browsers.*/

pre {
  font-family: monospace, monospace; /* 1 */
  font-size: 1em; /* 2 */
}

/* Text-level semantics */
/* Remove the gray background on active links in IE 10. */

a {
  background-color: transparent;
}

/* 1. Remove the bottom border in Chrome 57-
 * 2. Add the correct text decoration in Chrome, Edge, IE, Opera, and Safari.*/

abbr[title] {
  border-bottom: none; /* 1 */
  text-decoration: underline; /* 2 */
  text-decoration: underline dotted; /* 2 */
}

/* Add the correct font weight in Chrome, Edge, and Safari.*/

b,
strong {
  font-weight: bolder;
}

/* 1. Correct the inheritance and scaling of font size in all browsers.
 * 2. Correct the odd `em` font sizing in all browsers.*/

code,
kbd,
samp {
  font-family: monospace, monospace; /* 1 */
  font-size: 1em; /* 2 */
}

/**
 * Add the correct font size in all browsers.
 */

small {
  font-size: 80%;
}

/**
 * Prevent `sub` and `sup` elements from affecting the line height in
 * all browsers.
 */

sub,
sup {
  font-size: 75%;
  line-height: 0;
  position: relative;
  vertical-align: baseline;
}

sub {
  bottom: -0.25em;
}

sup {
  top: -0.5em;
}

/* Embedded content */
/* Remove the border on images inside links in IE 10.*/

img {
  border-style: none;
}

/* Forms */
/* 1. Change the font styles in all browsers.
 * 2. Remove the margin in Firefox and Safari.*/

button,
input,
optgroup,
select,
textarea {
  font-family: inherit; /* 1 */
  font-size: 100%; /* 1 */
  line-height: 1.15; /* 1 */
  margin: 0; /* 2 */
}

/*Show the overflow in IE.
 * 1. Show the overflow in Edge.*/

button,
input { /* 1 */
  overflow: visible;
}

/*Remove the inheritance of text transform in Edge, Firefox, and IE.
 * 1. Remove the inheritance of text transform in Firefox.*/

button,
select { /* 1 */
  text-transform: none;
}

/*Correct the inability to style clickable types in iOS and Safari.*/

button,
[type="button"],
[type="reset"],
[type="submit"] {
  -webkit-appearance: button;
}

/* Remove the inner border and padding in Firefox.*/

button::-moz-focus-inner,
[type="button"]::-moz-focus-inner,
[type="reset"]::-moz-focus-inner,
[type="submit"]::-moz-focus-inner {
  border-style: none;
  padding: 0;
}

/* Restore the focus styles unset by the previous rule.*/

button:-moz-focusring,
[type="button"]:-moz-focusring,
[type="reset"]:-moz-focusring,
[type="submit"]:-moz-focusring {
  outline: 1px dotted ButtonText;
}

/* Correct the padding in Firefox.*/

fieldset {
  padding: 0.35em 0.75em 0.625em;
}

/**
 * 1. Correct the text wrapping in Edge and IE.
 * 2. Correct the color inheritance from `fieldset` elements in IE.
 * 3. Remove the padding so developers are not caught out when they zero out
 *    `fieldset` elements in all browsers.*/

legend {
  box-sizing: border-box; /* 1 */
  color: inherit; /* 2 */
  display: table; /* 1 */
  max-width: 100%; /* 1 */
  padding: 0; /* 3 */
  white-space: normal; /* 1 */
}

/* Add the correct vertical alignment in Chrome, Firefox, and Opera.
 */

progress {
  vertical-align: baseline;
}

/* Remove the default vertical scrollbar in IE 10+.*/

textarea {
  overflow: auto;
}

/* 1. Add the correct box sizing in IE 10.
 * 2. Remove the padding in IE 10.*/

[type="checkbox"],
[type="radio"] {
  box-sizing: border-box; /* 1 */
  padding: 0; /* 2 */
}

/*Correct the cursor style of increment and decrement buttons in Chrome.
 */

[type="number"]::-webkit-inner-spin-button,
[type="number"]::-webkit-outer-spin-button {
  height: auto;
}

/*1. Correct the odd appearance in Chrome and Safari.
 * 2. Correct the outline style in Safari.
 */

[type="search"] {
  -webkit-appearance: textfield; /* 1 */
  outline-offset: -2px; /* 2 */
}

/*Remove the inner padding in Chrome and Safari on macOS.
 */

[type="search"]::-webkit-search-decoration {
  -webkit-appearance: none;
}

/*1. Correct the inability to style clickable types in iOS and Safari.
 * 2. Change font properties to `inherit` in Safari.
 */

::-webkit-file-upload-button {
  -webkit-appearance: button; /* 1 */
  font: inherit; /* 2 */
}

/* Interactive*/
/* Add the correct display in Edge, IE 10+, and Firefox. */

details {
  display: block;
}

/* Add the correct display in all browsers.*/

summary {
  display: list-item;
}

/* Misc*/

/* Add the correct display in IE 10+.*/

template {
  display: none;
}

/* Add the correct display in IE 10.*/

[hidden] {
  display: none;
}
```

