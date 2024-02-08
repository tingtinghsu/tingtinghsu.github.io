---
title:  "javascript 函數"
preview: "css flexbox"
permalink: "/articles/2019-03-13-js_function"
date:   2019-03-13 10:56:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "javascript"  
comments: true
---

javascript function 函數  
`function`是可以重複利用的SOP，相同的功能寫成函數後就可以重複利用，讓程式碼更簡潔。
<!-- more -->

---
目錄:
* abstact
{:toc}

---


# 1. function: 重複利用的SOP

## 跟客人打招呼

```javascript
function sayHelloTo(customer) {
	console.log('Hi! ' + customer);
}

sayHelloTo('孫小美');
sayHelloTo('錢夫人');
sayHelloTo('阿土伯');
```

輸出:

```html
"Hi! 孫小美"
"Hi! 錢夫人"
"Hi! 阿土伯"
```

fuction名字: sayHelloTo
代入參數: customer
function裡面：做的事`(跟客人打招呼)`

```javascript
function sayHelloTo(customer) {
	console.log('Hi! ' + customer);
}
```

就算有SOP，仍必須主動做出`跟客人打招呼`這件事:

```javascript
sayHelloTo('客人的名字');
/*執行定義好的function，並代入字串*/
```

# 2. function 內加上條件判斷

eg. 檢查年齡

## 寫法1. 呼叫多次函數
```javascript
function isAdult(age) {
	if(age >= 18) {
		console.log(age+'歲，可以喝酒了！');
	}
	else
		console.log(age+'歲，好年輕呀！');
}

isAdult(17);
isAdult(18);

```

輸出:
```html
"17歲，好年輕呀！"
"18歲，可以喝酒了！"
```

## 寫法2. 換成使用者輸入的版本

```javascript
var age = prompt('請輸入年齡');
function isAdult(age) {
	if(age >= 18) {
		console.log(age+'歲，可以喝酒～');
	}
	else
		console.log(age+'歲，好年輕呀！');
}

isAdult(age);
```

輸出
```javascript
"15歲，好年輕呀！"
"33歲，可以喝酒～"
```

## 寫法3. 與true / false結合，傳出參數

因為console.log功能不包含回傳（只負責印出內容，但是回傳值是`undefined`），
所以我們寫一個`function`，用`true`明確的回傳結果。

```javascript
/*function + 包含true / false 條件判斷先寫好*/
function isAdult(age) {
	if(age >= 18) {
		return true;
	}
	else {
		return false;
	}
}

/*if true，條件判斷*/
var yourAge = 18;
if (isAdult(yourAge)) { /*如果為True*/
	console.log(yourAge+'歲，可以喝酒了！');
}
else {
	console.log(yourAge+'歲，好年輕呀！');
}
```

## 寫法4. 寫法3進化版: 使用者輸入的版本

```javascript
/*function + 包含true / false 條件判斷先寫好*/
function isAdult(age) {
	if(age >= 18) {
		return true; 
	}
	else {
		return false;
	}
}

/*if true，條件判斷*/
var yourAge = prompt('請輸入你的年紀：');
if (isAdult(yourAge)) { /*如果為True*/
	console.log(yourAge+'歲，可以喝酒了！');
}
else {
	console.log(yourAge+'歲，好年輕呀！');
}
```

# 3. function的 return回傳值

用console的方式，雖然能夠在螢幕上顯示出來，但沒有回傳值。

```bash
> console.log(`hi`)
hi
< undefined

> function abc() { return 100 }
< undefined
> abc ()
< 100
```

## 寫法5. 簡潔的function定義方法

```javascript
function isAdult(age) {
	if(age >= 18) {
		return true;
	}
	else {
		return false;
	}
}
```

等於

```javascript
function isAdult(age) {
	if(true) {
		return true; 
	} /*true重複，多餘邏輯*/
	else {
		return false;
	}
}
```

等於

```javascript
function isAdult(age) {
	return (age >= 18);
}
```

# 4. anonymous function匿名函數

## 寫法6. 改為匿名函數

```javascript=
function isAdult(age) {
	return (age >= 18);
}
```

改為沒有名字的`anonymous function`:

```javascript
var isAdult = function(age) {
	return (age >= 18);
```

## 匿名與非匿名比較

```javascript
function a1() {
	console.log('a1');
}

a2 = function() {
  console.log('a2');
}

a1();
a2();
```

輸出:

```bash
"a1"
"a2"
```

## Hoisting 變數提升

javascript會自動做變數提升，用var設定變數，
在某些情況下會呼叫不到這個變數。

```javascript
/* var abc, a2;  java根據下面程式設定abc與a2變數*/

function a1() {
	console.log('a1');
}

a2 = function() {
  console.log('a2');
}

a1();
a2();

var abc = 123;

```

如果把`a1()`, `a2()` 提到前面:

```javascript
a1();
// javascript會把變數提升 => var a2; // undefined
a2();

var abc, a2;

function a1() {
	console.log('a1');
}

a2 = function() {
  console.log('a2');
}

var abc = 123;
```

執行時會出現問題

```bash
"a1"
"error"
"TypeError: a2 is not a function
    at delobel.js:2:1
    at https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:13924
    at https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:10866"

```

# hello 和 hello() 的不同

hello是function，
hello()是執行函數的結果。

## 有回傳值的結果

```javascript
function hello() {}
undefined

function hello() {console.log('hi');}
undefined
> hello()
hi                            VM150:1

undefined

> hello
ƒ hello() {console.log('hi');}

```

## 沒有回傳值的結果

```javascript
function hello_return() {return 1};
undefined

hello_return();
1
hello_return;
ƒ hello_return() {return 1}
```

# BMI練習題，使用funtion改寫

```javascript
function bmiResult(height, weight) {
//
}
```

解法:

```javascript
function bmiResult(height, weight) {
	bmi = weight / ( height * height )
	return bmi;
}

var height = prompt('請輸入你的身高(公尺)：');
var weight = prompt('請輸入你的體重(公斤)：');
console.log( bmiResult(height, weight) );
```

# 閏年練習題，使用funtion改寫

```javascript
function isLeapYear(year) {
	
}
```

解法:

```javascript
function isLeapYear(year) {
	if (((year % 4 === 0) && (year % 100 !== 0)) || (year % 400 === 0)){
	return true;
}
else
	return false;
}

var year = prompt('請輸入年份');
if (isLeapYear(year)) {
	console.log( year +'年是閏年');
}
else {
	console.log( year +'年不是閏年');
}

```

輸出:

```javascript
"1980年是閏年"
"1990年不是閏年"
"2000年是閏年"
```

# 相關連結

[HackMD筆記](https://hackmd.io/nAM4U5BsSJ6acOLVG5_yhw?view) 