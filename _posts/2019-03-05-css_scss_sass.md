---
title:  "css: scss.sass"
preview: "css scss and sass"
permalink: "/articles/2019-03-05-css_scss_sass"
date:   2019-03-05 11:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  
comments: true
---

CSS: scss. sass  
`scss`和`sass`是具有巢狀結構的css，讓程式碼更易於管理。
<!-- more -->

---
目錄:
* abstact
{:toc}

---

scss. sass

* SCSS 官網: https://sass-lang.com/
* Koala: http://koala-app.com/

# scss 與 css 的相容性: 

scss必須要轉成css才能被瀏覽器判讀。

- codepan: 在`settings`設定`CSS Preprocessor`
- 本機: 使用koala


# 優點1. 巢狀結構

```css
article {
  background-color: lightgray;
  padding: 25px;
  
  h1 {
  font-size: 2em;
  font-weight: 300;
  color: purple;
  }
  
   p {
  font-size: 1.4em;
  font-weight: 300;
  }
}
```

# 優點2. 自訂變數

```css
$background: lightgray; /*設定背景色為灰色*/

article {
  background-color: $background;
  padding: 25px;
  
  h1 {
  font-size: 2em;
  font-weight: 300;
  color: purple;
  }
  
   p {
  font-size: 1.4em;
  font-weight: 300;
  }
}
```

# 相關連結

[HackMD筆記](https://hackmd.io/VR-rGD86Qs-Rs5SAal4c-w?view)  
[CodePen練習](https://codepen.io/tingtinghsu/pen/LaRMGj)  