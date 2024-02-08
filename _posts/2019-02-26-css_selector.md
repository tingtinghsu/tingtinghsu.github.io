---
title:  "css 標籤位置與選取器"
preview: "css selector"
permalink: "/articles/2019-02-26-css_selector"
date:   2019-02-26 10:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  
comments: true
---

2019年的2月開始，我花了2個月時間把前端的CSS和Javascript的實用語法整理成筆記。本篇要講的是CSS標籤位置與選取器。

<!-- more -->

---
目錄:
* abstact
{:toc}

---

# 1. css標籤

css所在的標籤位置，共有三種寫法。

## 1-1. 直接寫在html標籤`<>`裡

* html 語法

```html
<h1 style="color: red"; font-size: 100px>你好，世界!</h1>
```

---

## 1-2. 在html新增`<style>`標籤

* html 語法

```html
<title>I'm coding it 之 CSS 練習</title>
<h2>
 你好，貓貓
</h2>
<style>
    h2 {
      color: blue;
    }
  </style>
```

* 瀏覽器顯示結果

<html>
<title>I'm coding it 之 CSS 練習</title>
<h3>
 你好，貓貓
</h3>
<style>
    h3 {
      color: blue;
    }
  </style>
</html>  

---

## 1.3 `<link>`標籤

在html檔案裡引入css所在的檔案位置。
優點：當css語法變多的時候，易於維護。

* html 語法

```html
  <link rel="stylesheet" href="style.css">
```  

---

# 2. css選取器

CSS為什麼叫`Cascade`? 
因為優先順序很重要。講得越明確、越精準，效果的排序越前面。

## 2-1. 型態選取器 Type Selector

* html 語法

```html
<h1>你好</h1>
```

* css 語法

```css
h1 {
    color: red;
}
```

缺點：選取範圍太廣。
例如：h1標籤下的所有內容全部都會變成紅色。

---

## 2-2. 後代選取器 Descendant Selector

階層概念排列。
例如`article`裡面的`p`，`寫成article p`

* html 語法

```html
<article>
  <h2>附標</h2>
  <p>今天心情如何呢? 雖然很忐忑，但這也都是人生體驗的一部分唷！</p>
</article>
```

* css 語法

```css
article p {
    color: green;
}
```

* 瀏覽器顯示結果

<html>

  <article>

    <h2>本日運勢</h2>
    <p>今天心情如何呢? 雖然很忐忑，但這也都是人生體驗的一部分唷！</p>
  </article>

  <style>

article p {
      color: green;
  }
  </style>
</html>

  
---

## 2-3. 類別選取器 Class Selector `.`

* html 語法

```html
  <p class="nerv">今天心情如何呢? 雖然很緊張，但這也都是人生體驗的一部分唷！</p>
  
  <p class="frad">今天心情如何呢? 雖然很害怕，但這也都是人生體驗的一部分唷！</p>
```

* css 語法

```css
.nerv {
  color: pink
}

.frad {
  color: purple
}
```

精確度：
類別選取器 > 後代選取器。

---

## 2-4. ID選取器 ID Selector `#`

* html 語法

```html
  <p id="sad">今天心情如何呢? 雖然很難過，但這也都是人生體驗的一部分唷！</p>
```

* css 語法

```css
#sad {
  color: orange;
}
```

---

## 2-5. 混合型

混合Descendant Selector和Class Selector的表示法。

標籤`article`下的某個類別`.content`:

```css=
article .content {

}
```

混合Class Selector和ID Selector的表示法。

* html 語法

```html
  <p class="mood" id="worr">今天心情如何呢? 雖然很擔心，但也是人生的一部分唷！</p>
```

* css 語法

```css
.mood#worr {
  color: gray
}
```

# 相關連結

[HackMD筆記](https://hackmd.io/wlTDq6r6R3WFKX7dyzth2A?view)  
[CodePen練習](https://codepen.io/tingtinghsu/details/gEaOYE)  
[網頁作品](../public/note/cssdemo/01-selector.html){:target"_blank"}
