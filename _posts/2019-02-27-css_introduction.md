---
title:  "css 常用語法"
preview: "css introduction"
permalink: "/articles/2019-02-27-css_introduction"
date:   2019-02-27 10:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  
comments: true
---

CSS常用語法，包含`字體大小`，`字型`，`粗細`，`顏色`，`對齊`，`寬度與高度`等。
<!-- more -->

---
目錄:
* abstact
{:toc}

---

# 1. 字體大小

## `px`: pixels
```css
p {
  font-size: 30px; 
  
}
```
## `em`：element，根據上一層標籤倍數調整
```css
article {
  font-size: 30px;
}

p {
  font-size: 1.2em; 
  
}
```
原始html
```htmlembedded=
<article>
  <h2>重點</h2>
  <p>大家一起來學日文</p>
</article>

<footer>
  <p>&copy; 版權所有，翻譯不究</p>
</footer>
```
此時`footer`內的`p`標籤，字體大小不會受影響。
若要改變，必須在`css`加上`footer`設定：
```css
footer {
  font-size: 20px;  
}
```
`footer`內的`p`標籤的字體大小才會跟著做倍數改變。

## `rem`：root element，根據根標籤倍數調整
需設定`html`標籤的`css`
```css
html {
  font-size: 25px;
}
```
```css=
p {
  font-size: 1.2rem;
}
```
什麼單位比較好？選用相對單位的較好。
符合RWD響應式開發。

# 2. 字型

```css
p {
  font-family: 'Courier New', Courier, monospace;
}
```

# 3. 粗細
```css
p {
  font-weight: 100;  /*範圍100-900*/
  font-weight: normal;  /*正常400*/
  font-weight: bold;     /*粗體700*/
}
```
# 4. 對齊

```css
p {
  text-align: left;
  text-align: right;
  text-align: center;  /*置中對齊*/
  text-align: justify; /*分散對齊*/  
}
```

# 5. 顏色

## 原理
16進位
0, 1, 2, 3, 4, 5, 6, 7,
8, 9, A, B, C, D, E, F,

16進位  FF
10進位 255 (16*16-1=255)

RGB
`#FF|00|00` Red
`#00|FF|00` Green
`#00|00|FF` Blue

`#FF|FF|00` Red + Green = Yellow

## css顏色語法
```css
p {
  color: red;             /*紅色*/
  color: #ff0000;         /*紅色*/
  color: #f00;            /*紅色，兩位數字相同的可以省略*/  
  
  color: #000000;         /*白色*/  
  color: #000;            /*白色，兩位數字相同的可以省略*/  
  
  color: #ffffff;         /*黑色*/
  color: #fff;            /*黑色，兩位數字相同的可以省略*/  

  color: rgb(255, 0, 0);  /*紅色*/
  color: rgba(255, 0, 0, 0.7); /*紅色+0.7alpha不透明度*/
  background-color: yellow;    /*設定背景色為黃色*/
} 
```

# 6. 寬度與高度

```css
p {
  font-weight: normal;  /*範圍100-900*/  
  text-align: center; /*置中對齊*/
  color: rgba(255, 0, 0, 0.5); /*紅色 + 0.7的不透明度*/
  background-color: pink;
  
  width: 500px;
  height: auto; /*隨瀏覽器自動調整大小*/
}
```

注意：不是所有元素都能設定寬度與高度。

# 相關連結
[HackMD筆記](https://hackmd.io/ELSQzlWXRp238OBqsw3ttA?view)  
[CodePen練習](https://codepen.io/tingtinghsu/details/ZPbEGL)  
[網頁作品](../public/note/cssdemo/02-introduction.html){:target"_blank"}