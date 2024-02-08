---
title:  "javascript 變數"
preview: "css flexbox"
permalink: "/articles/2019-03-12-js_variable"
date:   2019-03-12 10:56:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "javascript"  
comments: true
---

Javascript的特性
1. 全域變數  
2. Automatic Semicolon Insertion (ASI) 自動加分號  
<!-- more -->

---
目錄:
* abstact
{:toc}

---


# 1. 變數名稱與型態

變數本身沒有型態，需要設定（如同在瓶子上貼上標籤）

```javascript
/*沒指定值, undefined*/
var name = "龍女僕";   /*文字*/
var power = 520000;  /*數字*/
var goodGuy = true; /*布林值*/

var hello; /*預設undefined*/
var ud = undefined;
/*it's not necessary to initialized 'ud' to 'undefined' */
var nl = null;
```

## 不同變數型態相連 => 轉型

```javascript
console.log('1+1'); // "1+1"
console.log(1+1); // 2
console.log(1+"1"); // "11" 自動轉型

console.log('hello' + 123); // "hello123" 數字被轉成文字
console.log('hello' + true); // "hellotrue" 自動轉型
console.log(123 + true); // 124 true自動轉型為1
console.log(123 + false); // 124 false自動轉型為0
console.log("123" + true); // 123true自動轉型
console.log(123 + undefined); // NaN, not a number
console.log([]+[]); // "" 空陣列+空陣列=空字串
console.log([]+{}); // [object Obeject] 陣列裡面放的物件
console.log({}+[]); // 0? 
console.log({}+{}); // "[object Object][object Object]" 物件+物件
```

# 2. 布林值

## true / false

```javascript
var a = true;
console.log(a); /*true*/
```

## 用邏輯判斷true或false

用一個變數，判斷另一變數是否符合條件:
```javascript
var age = 10;
var isAdult = (age >= 18);
console.log(isAdult);  /*false*/
```

# 3. 四則運算

## 先乘除後加減

```javascript
console.log(2+3*2); /* 6+2=8 */
console.log((2+3)*2); /* 5*2=10 */
```

# 4. 指定值

## a `=` a + 1

```javascript
var a = 1;
a = a + 1; /* +1 */
a += 1; /* 再+1 */
a ++ ; /* 再+1 */
console.log(a); /* 4 */
```

## `=` 給值 v.s. `==` 值相等

no

```javascript
var age = 8;

if (age == 10) {
	console.log('yes');
} else {
	console.log('no'); /* no */
}
```

yes

```javascript
var age = 8;

if (age = 10) {
	console.log('yes');
} else {
	console.log('no'); /* yes */
}
```

## `=` v.s. `==` v.s. `===` 值與型態相等

```javascript
console.log(1 == 1);
console.log(1 == "1"); /* 值(內容物)同, true */
console.log(1 === "1");  /* 型態不同, false */
```

## BMI練習題

```javascript
var baterWeight = 78;
var baterHeight = 1.75;
var bmi = baterWeight / (baterHeight * baterHeight)

if (bmi < 24) {
	console.log(bmi);
} else {
	console.log('bmi = '+ bmi +' 該運動了'); 
}
```

# 5. 流程控制

## if - else

選項只有兩個：
Yes or No
```javascript
var age = 10;

if (age === 10) {
	console.log('YES');
} else {
	console.log('NO');
}
```

已成年 or 未成年：
```javascript
var age = 20; /* 已成年 */

if (age >= 18) {
	console.log('已成年'); /*加括弧，增加可讀性*/
} else {
	console.log('未成年');
}
```

## if - else if - else

```javascript
var age = 16; /* 青少年 */

if (age >= 18) {
	console.log('已成年');
}
else if ((age < 18) && (age >= 12)) {
	console.log('青少年');
}
else {
	console.log('小朋友');
}

```

## 三元運算子 `(條件) ? 符合時 : 不符合時`

```javascript
var age = 20;
var isAdult;

if (age >= 18) {
	isAdult = true;
} else {
	isAdult = false;
}
console.log(isAdult);
```

第4行到第8行，可利用三元運算子省略

```javascript
var age = 20;
var isAdult = (age >= 18) ? true : false;

console.log(isAdult);
```

## switch

```javascript
var gender = 0; /* 不想說 */

switch(gender) {
	case 1:
		console.log('Male');
		break;

	case 2:
		console.log('Female');
		break;

	default:
		console.log('不想說');
		break;
}
```

## true / false 判斷

```javascript
var guesswhat;

switch(guesswhat) {
	case 0:
		console.log('0');
		break;

	case null:
		console.log('null');
		break;

	case "":
		console.log("");
		break;

	default:
		console.log(guesswhat); /*undefined*/
		break;
}

```

## 避免放進條件式的5種變數型態

1. 0
2. undefined
3. null
4. 空字串
5. NaN

以上皆會被判定成false  

```javascript
var isTrue; /* undefined */

if(isTrue) { /* 條件成立 */
	console.log('true');
}
else {
	console.log('false'); /* undefined印出為false */
}

```

使用NaN

```javascript
var isTrue = NaN;

if(isTrue) {
	console.log('true');
}
else {
	console.log('false'); /* NaN印出為false */
}

```

# promt(): 讓使用者輸入變數

## 閏年練習題

閏年判斷方式公式：
年份可以被4整除而不可被100整除，但又可以被400整除。
https://zh.wikipedia.org/wiki/%E9%97%B0%E5%B9%B4


```javascript
var year = prompt('請輸入年份');
if (((year % 4 === 0) && (year % 100 !== 0)) || ((year % 100 === 0) && (year % 400 === 0))){
	console.log(year + ' 是閏年');
}
else
	console.log(year + ' 不是閨年');
```

輸出:

```html
"1980 是閏年"
"1900 不是閨年"
"2000 是閏年"
```

程式簡寫：
（可以被400整除為零，則一定可以被100整除。）

```javascript
var year = prompt('請輸入年份');
if (((year % 4 === 0) && (year % 100 !== 0)) || (year % 400 === 0)){
	console.log(year + ' 是閏年');
}
else
	console.log(year + ' 不是閨年');
```

# 相關連結

[HackMD筆記](https://hackmd.io/UeUrL6FjSPm5173AjCsRsQ?view) 