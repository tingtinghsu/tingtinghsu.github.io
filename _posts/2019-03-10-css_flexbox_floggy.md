---
title:  "css: flexbox遊戲-青蛙攻略"
preview: "css flexbox floggy"
permalink: "/articles/2019-03-10-css_flexbox_floggy"
date:   2019-03-10 10:55:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "css"  
comments: true
---

css: flexbox 進階排版  
[flexbox floggy](https://flexboxfroggy.com/)是一款讓我們學習`flexbox`的遊戲，本篇將會解說遊戲攻略。
<!-- more -->

---
目錄:
* abstact
{:toc}

---

# justify-content

```
請用justify-content屬性指引青蛙到右邊的荷葉上。這個屬性可以水平對齊元素，並且接受一下幾個參數:
* flex-start: 元素和容器的左端對齊。
* flex-end: 元素和容器的右端對齊。
* center: 元素在容器裏居中。
* space-between: 元素之間保持相等的距離。
* space-around: 元素周圍保持相等的距離。
```

1. justify-content: flex-end;會將青蛙移到結尾端(右邊)。

```css
#pond {
  display: flex;
  justify-content: flex-end;
}
```

2.justify-content: center;會將青蛙移到中間。

```css
#pond {
  display: flex;
  justify-content: center;
}
```

3.justify-content: space-around;分散對齊，每個元素有自己的空間。

```css
#pond {
  display: flex;
  justify-content: space-around;
}
```

4.justify-content: space-between;分散對齊，兩邊的元素靠近邊界。

```css
#pond {
  display: flex;
  justify-content: space-between;
}
```

# align-items

```
現在用align-items來幫助青蛙們到池塘的底部。這個CSS屬性縱向對齊元素並且可選以下幾個值：

flex-start: 元素與容器的頂部對齊。
flex-end: 元素與容器的底部對齊。
center: 元素縱向居中。
baseline: 元素在容器的基線位置顯示。
stretch: 元素被拉伸以填滿整個容器。
```

5.align-items: 對齊底部。

```css
#pond {
  display: flex;
  align-items: flex-end;
}
```

6.justify-content / align-items 水平對齊 + 垂直對齊 = 置中對齊

```css
#pond {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

7.align-items / justify-content 底部置中

```css
#pond {
  display: flex;
  align-items: flex-end;
  justify-content: space-around;
}
```

# flex-direction

```
青蛙們需要和他們的荷葉保持對應的順序。我們可以用flex-direction屬性。這個CSS屬性定義了元素在容器裏擺放的方向，並且接受這些值：

row: 元素擺放的方向和文字方向一致。
row-reverse: 元素擺放的方向和文字的方向相反。
column: 元素從上放到下。
column-reverse: 元素從下放到上。
```

8.元素的擺放和文字方向相反

```css
#pond {
  display: flex;
  flex-direction: row-reverse;
}
```

9.元素的擺放和文字方向垂直

```css
#pond {
  display: flex;
  flex-direction: column;
}
```

10.元素的擺放和文字方向相反，並靠結尾端（左）

```css
#pond {
  display: flex;
  flex-direction: row-reverse;
  justify-content: flex-end;
}
```

11.元素的擺放和文字方向垂直，並靠結尾端（下）

```css
#pond {
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
}
```

12.元素的擺放和文字方向垂直，順序反轉，分散對齊且靠邊

```css
#pond {
  display: flex;
  flex-direction: column-reverse;
  justify-content: space-between;
}
```

13.元素的擺放和文字方向相同，順序反轉，置中對齊且靠直欄的結尾端

```css
#pond {
  display: flex;
  flex-direction: row-reverse;
  justify-content: center;
  align-items: flex-end;
}
```

# order

```
有時候僅僅調轉列或行的方向是不夠的。在這些情況，我們可以設置單個元素的order屬性。元素的屬性默認值爲0，但是我們設置這個屬性爲正數或負數。

用order來調整青蛙的順序。
```

14.黃色元素排最後

```css
#pond {
  display: flex;
}

.yellow {
order: 1;
}
```

15.紅色元素排最前

```css
#pond {
  display: flex;
}

.red {
order: -1;
}
```

# align-self

```
另一個你可以使用的控制單個元素的屬性是align-self。這個屬性接受和align-items一樣的那些值。
```

16.其中一個元素自己在直欄對齊

```css
.yellow {
align-self: flex-end;
}
```

17.其中一組同一屬性的元素排序後，再直欄對齊

```css
.yellow {
order: 1;
align-self: flex-end;
}
```

# flex-wrap

```
這些青蛙都擠到一列了。用flex-wrap屬性把它們分散看看。這個屬性接受這些值：

nowrap: 所有的元素都在一行。
wrap: 元素自動換成多行。
wrap-reverse: 元素自動換成逆序的多行。
```

18.若太擠，自動換行

```css
#pond {
  display: flex;
  flex-wrap: wrap;
}
```

19.先換方向成直欄，再自動換行

```css
#pond {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
}
```

#flex-flow

flex-flow = flex-direction + flex-wrap 

```
flex-direction和flex-wrap兩個屬性經常會一起使用，所以有縮寫屬性flex-flow。這個縮寫屬性接受兩個屬性的值，兩個值中間以空格隔開。

舉個例子，你可以用flex-flow: row wrap去設置列並自動換行。
```

20.flex-flow 換方向成直欄 + 自動換行
```css
#pond {
  display: flex;
  flex-flow: column wrap;
}
```

# align-content

```
青蛙們在池塘裏到處都是，然而荷葉都在頂部。你可以使用align-content來決定行與行之間隔多遠。這個屬性接受這些值：

flex-start: 多行都集中在頂部。
flex-end: 多行都集中在底部。
center: 多行居中。
space-between: 行與行之間保持相等距離。
space-around: 每行的周圍保持相等距離。
stretch: 每一行都被拉伸以填滿容器。

align-content決定行之間的間隔，而align-items決定元素整體在容器的什麼位置。只有一行的時候align-content沒有任何效果。
```

21.讓多行靠向頂部

```css
#pond {
  display: flex;
  flex-wrap: wrap;
  align-content: flex-start;
}
```

22.讓多行靠向底部

```css
#pond {
  display: flex;
  flex-wrap: wrap;
  align-content: flex-end;
}
```

23.元素方向直欄，元素順序相反，多行置中
```css
#pond {
  display: flex;
  flex-wrap: wrap;
  flex-direction: column-reverse;
  align-content: center;
}
```

```
Bring the frogs home one last time by using the CSS properties you've learned:

justify-content
align-items
flex-direction
order
align-self
flex-wrap
flex-flow
align-content
```

24.flex-wrap: warp-reverse; 元素反過來換行

```css
flex-wrap: warp-reverse;
flex-direction: column-reverse;
justify-content: center;
align-content: space-between;
```

# 相關連結

flexbox 進階排版筆記
https://hackmd.io/TsygxXfBR4i9PnV8aS5KvA?view
flexbox 進階排版範例
https://codepen.io/tingtinghsu/full/EMmNxK