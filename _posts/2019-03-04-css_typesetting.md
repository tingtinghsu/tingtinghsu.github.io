---
title:  "css: 新聞排版"
preview: "css typesetting"
permalink: "/articles/2019-03-04-css_typesetting"
date:   2019-03-04 10:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  
comments: true
---

CSS: 新聞排版  
在這篇的筆記裡，學習用css設計版型，分割出`導覽列`、`內容`等欄位。
<!-- more -->

---
目錄:
* abstact
{:toc}

---

# 先做出版面分割畫面
[whimsical](https://whimsical.co/TFic8Pc3Ku3bymWAtoyRLT) 是個好用的切版網站。


分割步驟: 
1. 導覽列
2. 區塊: 內容 + 廣告欄位


# 1. nav導覽列

html
```htmlmixed=
<nav>
<a href="#">首頁</a>
<a href="#">政治</a>
<a href="#">論壇</a>
<a href="#">財經</a>
<a href="#">娛樂</a>
<a href="#">運動</a>
</nav>  
```
css : setting 為 normalized 
```css
nav {
  background-color: #00ccc4; /*參考新聞版面真正的顏色*/
  padding: 8px; /*很擠的話用padding*/
}
```

## nav改良版

html 
1. 讓 nav 變成 list (用`ul` `li`標籤)

css 
1. 用`list-syle`把`點`消掉; 
2. 用`inline-block`變成一排，以改變每個單字（block）中間的間距
3. 調整字體大小和粗細，`text-decoration`移除超連結的底線

html
```htmlmixed=
<nav>
  <ul>
    <li><a href="#">首頁</a></li>
    <li><a href="#">政治</a></li>
    <li><a href="#">論壇</a></li>
    <li><a href="#">財經</a></li>
    <li><a href="#">娛樂</a></li>
    <li><a href="#">運動</a></li>
  </ul>
</nav>  
```

css
```css
nav {
  background-color: #00ccc4;
}

nav ul {
  list-style: none;
}

nav li {
  display: inline-block;
  padding: 15px;
}

nav a {
  color: white;
  font-size: 1em;
  font-weight: 200;
  text-decoration: none;
}
```

# 2. section 區塊

## 2.1 main content 主內容 = content + aside 

html: 利用命名class區分內文與廣告欄位
```htmlmixed=
<section class="main-content">
  <section class="content">內文</section>
  <aside>廣告</aside>  
</section>
```
css: `main-content`設定96%不要全滿，再利用`margin`的左右`auto`調整成為置中
```css
.main-content {
  /*background-color: orange;*/
  width: 96%;
  margin-left: auto;
  margin-right: auto;
}
```

## 2.2 content 內容 & aside 廣告欄位

調整重點:
1. %百分比: 分別占main-content的比例
2. float: 飄浮起來，都靠左邊
3. 兩個欄位與內文margin-right的百分比，相加需為100%

```css
.content {
  /*background-color: lightgreen;*/
  width: 69%;
  float: left;
  margin-right: 1%;
}

aside {
  /*background-color: pink;*/  
  width: 30%;
  float: left; 
}
```

## 2.3 content = top-content + sub-content

### 2.3.1 top-content: 被包含在content裡

```htmlmixed
  <section class="content">
    <section class="top-content">
      <img src="https://fakeimg.pl/200x100/?retina=1&text=ないよう&font=noto">
      <ul>
        <li>足球</li>
        <li>籃球</li>
        <li>桌球</li>
        <li>羽球</li>
        <li>排球</li>
        <li>網球</li>
      </ul>
    </section>  
  </section>
```
css
1. `.top-content img` 圖片漂浮靠左
2. `.top-content ul` 新聞列表不要點點
3. `.top-content li` 新聞標題行調整高度
4. `.top-content li a` 新聞超連結字體、顏色，不含底線



```css
/*top content*/
.top-content img{
 float: left;
 height: 200px;
 margin-right: 10px;
}

.top-content ul {
  /*float: left;*/
  list-style: none;
}

.top-content li {
  line-height: 22px;
}

.top-content li a {
  text-decoration: none;
  font-size: 1.1em;
  color: #747676;
}
```

### 2.3.2 sub-content

兩欄式 50% 50%

```htmlmixed
  <section class="content">
    <section class="top-content">
      <img src="https://fakeimg.pl/200x100/?retina=1&text=ないよう&font=noto">
      <ul>
        (略)       
      </ul>
    </section>
    <section class="sub-content">
      <h3>棒球 > </h3>
        <img src="https://fakeimg.pl/100x50/?retina=1&text=やきゅう&font=noto">
      <ul>
        (略) 
      </ul>
  </section>
    <section class="sub-content">      
      <h3>籃球 > </h3>
        <img src="https://fakeimg.pl/100x50/?retina=1&text=バスケットボール&font=noto">
      <ul>
        (略) 
      </ul>
    </section>
    <div class="clearfix"></div>
  </section>

```
  <section class="content">
    <section class="top-content">
      <img src="https://fakeimg.pl/200x100/?retina=1&text=ないよう&font=noto">
      <ul>
        <li><a href="#">足球新聞</a></li>
        <li><a href="#">籃球新聞</a></li>
        <li><a href="#">桌球新聞</a></li>
        <li><a href="#">羽球新聞</a></li>
        <li><a href="#">排球新聞</a></li>
        <li><a href="#">網球新聞</a></li>
        <li><a href="#">壁球新聞</a></li> 
        <li><a href="#">棒球新聞</a></li>
        <li><a href="#">壘球新聞</a></li>         
      </ul>
    </section>
    <section class="sub-content">
      <h3>棒球 > </h3>
        <img src="https://fakeimg.pl/100x50/?retina=1&text=やきゅう&font=noto">
      <ul>
        <li><a href="#">林子偉上場獲保送</a></li>
        <li><a href="#">張育成先發守二壘</a></li>
        <li><a href="#">巨人迷大吼馬恰多</a></li>
        <li><a href="#">波拉斯非浪得虛名</a></li>
        <li><a href="#">敲出帶有打點安打</a></li>
        <li><a href="#">基特的合約最超值</a></li>
      </ul>
  </section>
    <section class="sub-content">      
      <h3>籃球 > </h3>
        <img src="https://fakeimg.pl/100x50/?retina=1&text=バスケットボール&font=noto">
      <ul>
        <li><a href="#">柯瑞關鍵罰球失手</a></li>
        <li><a href="#">璞園贏達欣奪連勝</a></li>
        <li><a href="#">老鷹超級新秀無言</a></li>
        <li><a href="#">勇士神逆轉七六人</a></li>
        <li><a href="#">史上最棒投籃射手</a></li>
        <li><a href="#">吳岱豪灌籃猛扣球</a></li>
      </ul>
    </section>
    <div class="clearfix"></div>
  </section>
  
  css 重點
  1. `.sub-content ` 內容各占50%
  2. `.sub-content img`兩欄的照片之間空出一點空間
  3. `.sub-content li` 調行距
  4. `.sub-content a` 連結顏色.不含底線

```css
  /* sub-content */
.sub-content {
  /*background-color: lightblue;*/
  width: 50%;
  float: left;
}

.sub-content img {
  width: 90%;
}

.sub-content ul {
  /*list-style: none;*/
  font-weight: 300;
}
.sub-content li {
  line-height: 20px;
}

.sub-content a {
  text-decoration: none;
  color: #757676
}
```

## 2.4 aside 廣告欄位

```htmlmixed
<aside>
  <h3>廣告</h3> 
  <img src="https://fakeimg.pl/100x60/?retina=1&text=こうこく&font=noto">
  
  <h3>熱門運動新聞</h3>
   <ol>
        (略)     
   </ol>
 </aside>

```

 <aside>
  <h3>廣告</h3> 
  <img src="https://fakeimg.pl/100x60/?retina=1&text=こうこく&font=noto">
  <h3>熱門運動新聞</h3>
   <ol>
     <li><a href="#">王柏融續扛4番</a></li>
     <li><a href="#">高苑誤植攻守名單</a></li>
     <li><a href="#">平鎮裁定勝闖冠軍戰</a></li>
     <li><a href="#">台電男排達成例行賽</a></li>
     <li><a href="#">中職歷年20號戰將</a></li>
     <li><a href="#">富邦賞金酒4連敗</a></li>
     <li><a href="#">湖人不敵太陽終結連勝</a></li>
     <li><a href="#">盧卡庫雙響佩雷拉傳射</a></li>
     <li><a href="#">曼聯3-2逆轉南安普頓</a></li>
     <li><a href="#">台啤末節發威奪勝</a></li>     
   </ol>
 </aside>
 
# 相關連結

[HackMD筆記](https://hackmd.io/ONwTQ5YpQJ6eN6hfzpeX-Q?view)  
[CodePen練習](https://codepen.io/tingtinghsu/full/bZeRBm)  
[網頁作品](../public/note/cssdemo/05-typesetting.html){:target"_blank"}

