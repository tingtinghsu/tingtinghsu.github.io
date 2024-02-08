---
title:  "javascript blackjack game"
preview: "javascript OOP"
permalink: "/articles/2019-03-26-js_blackjack"
date:   2019-03-26 09:56:00
layout: post
tags:
  - "note"  
  - "front-end"
  - "javascript"  
comments: true
---

本篇會使用`jQuery` + `ES6`寫撲克牌小遊戲21點。一起來玩玩看吧！  

<!-- more -->
<[點我進入網頁版完成品](../public/note/jsdemo/24-blackjack.html)>

---
目錄:
* abstact
{:toc}

---

# 前置作業: 確定遊戲規則

* 發2張牌給玩家，發一張牌給莊家
* 玩家決定再拿牌(hit)或打住(stand)
* 玩家選擇stand後輪到莊家
* 玩家或莊家先達21點者贏
* 超過21點輸，遊戲結束
* 玩家如果跟莊家同點數，平手，遊戲結束
* 如果連發5張都還沒超過21點，也算是贏，遊戲結束
* 不論花色，1-10點代表牌面上數字，J, Q, K表示10點，A表示1或11點

# 遊戲點數試算

* [3, J] = 3 + 10 = 13點
* [A, 2, 3] = 11 + 2 + 3 = 16點
* [6, 8, A] = 6 + 8 + 1(因為算11點就爆了) = 15點
* [A + K] = 11 + 10 = 21點


# Single Responsibility Principle (SRP)

單一職責原則：一個函數只做一件事

# Part 1. 準備遊戲

## A. 初始化卡牌 `initCards()`

0.啟動

```javascript
$(document).ready {

}
```

1.為尚未翻開的牌面設計圖案

```javascript
$(document).ready {
    initCards();
}
```

2.抓html內的元素，換圖案

* 先把功能寫出來

javascript版本

```javascript
//莊家牌
$(document).ready(function(){
  // 抓所有div為card
  let alldealerCards = 
  alldealerCards.forEach( card => {
    card.innerHTML = '㊎';
  });

//玩家牌
  let allyourCards = document.querySelectorAll('.yourCard');
  allyourCards.forEach( card => {
    card.innerHTML = '㊖';
  });
});
```

* 獨立為function `initCards()`

```javascript
// 程式碼寫在這裡!
$(document).ready(function(){
  initCards();
});

//把換牌面設計的功能獨立出來
function initCards(){
    // 抓所有div為card
    let alldealerCards = document.querySelectorAll('.dealerCard');
    alldealerCards.forEach( card => {
      card.innerHTML = '㊎';
    });
  
    let allyourCards = document.querySelectorAll('.yourCard');
    allyourCards.forEach( card => {
      card.innerHTML = '㊖';
    });
}
```

把這段javascript版本註解掉，改寫成jQuery版本

```javascript
let alldealerCards = document.querySelectorAll('.dealerCard');
alldealerCards.forEach( card => {
  card.innerHTML = '㊎';
});

let allyourCards = document.querySelectorAll('.yourCard');
allyourCards.forEach( card => {
  card.innerHTML = '㊖';
});
```

jQuery版本

```javascript
$('.card div').html('㊎');
$('.yourCard').html('㊖');
```

* 小結

```javascript
$(document).ready(function(){
  initCards();
});

function initCards(){ 
  $('.dealerCard').html('㊎');
  $('.yourCard').html('㊖');

}
```

## B. 初始化按鈕 `initButtons()`

1.html按鈕區的id: 寫在`action-new-game`裡：

```html
<footer class="action">
        <input type="button" id="action-hit" class="action-button" value="Hit!" disabled>
        <input type="button" id="action-stand" class="action-button" value="Stand" disabled>
        <input type="button" id="action-new-game" class="action-button" value="New Game">
      </footer>
```

2.`New Game`按鈕: addEventListener

javascript  

* addEventListener聽`click`的動作，用`console.log`測試

```javascript
$(document).ready(function(){
  initCards();

// 綁定在New Game按鈕上面
  document.querySelector('#action-new-game').addEventListener('click', evt => {

    console.log('hi');
  });
});
```

* 初始化按鈕  

```javascript
initButtons();
```

* 寫成獨立的function `initButtons(){}`:

```javascript
$(document).ready(function(){
  initCards();
  initButtons();
});

function initButtons(){
    document.querySelector('#action-new-game').addEventListener('click', evt => {
        console.log('hi');
    });
}
```

jQuery版本  

把這段javascript寫法
```javascript
  document.querySelector('#action-new-game').addEventListener('click', evt => {

  });
```

改寫為 jQuery寫法

```javascript
  $('#action-new-game').click( function(evt) {

  });
```

再簡寫為箭頭函數  
複習: [js筆記：函數 function](https://hackmd.io/nAM4U5BsSJ6acOLVG5_yhw)

```javascript
  $('#action-new-game').click( evt => {

  });
```

* 小結

```javascript
$(document).ready(function(){
  initCards();
  initButtons();
});

// 把換牌面設計的功能獨立出來
function initCards(){
//  抓所有div為card  
    $('.dealerCard').html('㊎');
    $('.yourCard').html('㊖');
}

// 按完按鈕，重啟新牌局
function initButtons() {
  $('#action-new-game').click( evt => {
    console.log('hi');
  });
}
```

## C. 啟動遊戲 `newGame()`

1.`initButtons()` 串連到 `newGame()`
寫出`newGame()`架構:

```javascript
  function newGame() {
     console.log('new game');
  }
```

2.讓`initButtons()`去呼叫

```javascript
function initButtons() {
  $('#action-new-game').click( evt => {
    newGame();
  });
}
```

因為只有一行，可以省略大括弧:

```javascript
function initButtons() {
  $('#action-new-game').click( evt => newGame() );
}
```

* 小結

```javascript
$(document).ready(function(){
  initCards();
  initButtons();
});

// 把換牌面設計的功能獨立出來
function initCards(){
  $('.dealerCard').html('㊎');
  $('.yourCard').html('㊖');
}

// 按完按鈕，重啟新牌局
function initButtons() {
  $('#action-new-game').click( evt => newGame() );
}

function newGame() {
  console.log('new game');
}
```

## D. 建立牌組 `buildDeck()`

開始和朋友玩撲克牌遊戲的條件：**首先要有一副牌!!**

1.4種花色，每一種花色有13張牌 = 52張牌
=> 兩層for迴圈

```javascript
function buildDeck(){
    for(let suit = 1; suit <= 4; suit++) {
      for(let number = 1; number <= 13; number++){

  }
}
```

2.用物件導向OOP做出卡牌的模組

```javascript
//用OOP做卡牌
class Card {
  constructor(suit, number){
    this.suit = suit;
    this.number = number;
  }
}
```

3.完用模組做牌，迴圈內跑

```javascript
let c = new Card(suit, number); 

```

4.建立牌箱陣列（建了牌組之後，要有容器放牌）

```javascript
let deck[];
```

5.迴圈內，把產生的物件用`push()`方法塞進陣列

```javascript
deck.push(c);
```

6.在`newGame()`裡檢驗是否會回傳建立卡牌

```javascript
function newGame() {
  console.log(buildDeck());
}
```

* 小結

```javascript
function buildDeck(){
  let deck = []; // 建立空陣列放牌

    for(let suit = 1; suit <= 4; suit++) {
      for(let number = 1; number <= 13; number++){
        let c = new Card(suit, number); //做出52種牌組
        deck.push(c); //塞進去盒子
    }
  }

  return deck;
}

//用OOP做卡牌
class Card {
  constructor(suit, number){
    this.suit = suit;
    this.number = number;
  }
}

function newGame() {
  console.log(buildDeck());
}
```

console
```javascript
0: Object { suit: 1, number: 1 }
1: Object { suit: 1, number: 2 }
2: Object { suit: 1, number: 3 }
3: Object { suit: 1, number: 4 }
4: Object { suit: 1, number: 5 }
5: Object { suit: 1, number: 6 }
6: Object { suit: 1, number: 7 }
7: Object { suit: 1, number: 8 }
8: Object { suit: 1, number: 9 }
...
48: Object { suit: 4, number: 10 }
49: Object { suit: 4, number: 11 }
50: Object { suit: 4, number: 12 }
51: Object { suit: 4, number: 13 }
```

## E. 洗牌 `shuffle()`

javascript並沒有洗牌的功能
google 關鍵字: javascript array shuffle

1.尋找他人寫過的function

```javascript
// 洗牌
//https://gomakethings.com/how-to-shuffle-an-array-with-vanilla-js/
var shuffle = function (array) {

	var currentIndex = array.length;
	var temporaryValue, randomIndex;

	// While there remain elements to shuffle...
	while (0 !== currentIndex) {
		// Pick a remaining element...
		randomIndex = Math.floor(Math.random() * currentIndex);
		currentIndex -= 1;

		// And swap it with the current element.
		temporaryValue = array[currentIndex];
		array[currentIndex] = array[randomIndex];
		array[randomIndex] = temporaryValue;
	}

	return array;

};
```

2.把建好的牌組洗牌

```javascript
function newGame() {
  console.log( shuffle ( buildDeck() ) );
}
```

* 小結

console內成功印出52張亂數牌

```javascript
0: Object { suit: 3, number: 13 }
1: Object { suit: 4, number: 13 }
2: Object { suit: 3, number: 3 }
3: Object { suit: 2, number: 13 }
4: Object { suit: 4, number: 11 }
5: Object { suit: 2, number: 12 }
6: Object { suit: 3, number: 12 }
7: Object { suit: 2, number: 2 }
8: Object { suit: 1, number: 6 }
9: Object { suit: 4, number: 3 }
10: Object { suit: 3, number: 8 }
```

## F. 發牌給玩家 `deal()`

### 重點：就算是簡單的一行程式碼，也有包成函數的價值!

1.啟動遊戲的時候，就有 `deck` = 那疊洗好的牌

```javascript
// Part 3 啟動遊戲:
function newGame() {
  //console.log(shuffle(buildDeck()));
  deck = shuffle( buildDeck() );
}
```

2.按照順序發牌: 用`shift();`取得第一張牌 

```javascript
// Part 6 發牌:
function deal() {
  return deck.shift();
}
```

3.定義莊家和玩家的牌組

```javascript
let yourDeck = [];
let dealerDeck = [];
```

4.從啟動遊戲的funciton發牌，push一張給玩家，一張給莊家，再一張給玩家

```javascript
// Part 3 啟動遊戲:
function newGame() {
  //console.log(shuffle(buildDeck()));
  deck = shuffle(buildDeck());
  
  yourDeck.push(deal());
  dealerDeck.push(deal());
  yourDeck.push(deal()); 
}
```

## G. 增加卡牌功能 `class Card{}`

1.計算卡牌的點數 `cardPoint() {}`

```javascript
  cardPoint() {
    switch(this.number) {
      case 1:
        return 11;
      case 11:
      case 12:
      case 13:
        return 10;
      defult:
        return this.number;
  }
```

2.呈現花色樣式

```javascript
  cardSuit() {
    switch(this.suit) {
      case 1:
        return '♠';
      case 2:
        return '♥';
      case 3:
        return '♦';
      case 4:
        return '♣';
    }
  }
```

## H. 畫面顯示牌組 `renderGameTable()`

Show Hand吧！

1.用jQuery把HTML值指定為成玩家抽到的牌，並顯示出來

```javascript
// Part 8 在畫面看到發牌的樣子
function renderGameTable() {
  //用迴圈的方式把已經抽到的牌翻出來
  yourDeck.forEach((card, i) => {
    //把抓到的卡牌 0, 1, 2, 3, 4 用索引值[i]塞進去`.{i + 1}`
  let theCard = $(`#yourCard${i + 1}`); 
  //兩層$, 內層放索引值，外層代表jQuery的取值方式
    theCard.html('A'); //測試
  });
}
```

2.`cardShowNumber()`卡牌數字轉成A, J, Q, K

```javascript
  cardShowNumber() {
    switch(this.number) {
      case 1:
       return 'A';
      case 11:
       return 'J';
      case 12:
       return 'Q';
      case 13:
       return 'K';
      defult:
       return this.number;  
    }
  }
```

3.秀出卡牌

```javascript
// Part 8 在畫面看到發牌的樣子
function renderGameTable() {
  //用迴圈的方式把已經抽到的牌翻出來
  yourDeck.forEach((card, i) => {
    //把抓到的卡牌 0, 1, 2, 3, 4 用索引值[i]塞進去`.{i + 1}`
  let theCard = $(`#yourCard${i + 1}`); 
  //兩層$, 內層放索引值，外層代表jQuery的取值方式
    theCard.html(card.cardShowNumber());     
  });
}
```

4.秀出花色

如何把span的欄位抓出來?

`<div class="card"><span></span><div class="yourCard" id="yourCard1"></div></div>`

### 把某標籤的前一個標籤抓出來`.prev()`

抓`<div class="yourCard></div>"`的前一個元素的寫法:

```javascript
theCard.prev().html(card.cardSuit())：
```

莊家與玩家的牌組：

```javascript
function renderGameTable() {
  //用迴圈的方式把已經抽到的牌翻出來
  yourDeck.forEach((card, i) => {

  let theCard = $(`#yourCard${i + 1}`); 
    theCard.html(card.cardShowNumber());
    theCard.prev().html(card.cardSuit());
  });

  dealerDeck.forEach((card, i) => {
    let theCard = $(`#dealerCard${i + 1}`); 
      theCard.html(card.cardShowNumber());
      theCard.prev().html(card.cardSuit());
  });
}
```

## I. 計算卡牌目前得分 `calcPoint(deck)`

1.迴圈算分數

```javascript
//Part9 用迴圈算牌的分數
function calcPoint(deck) {
  let point = 0;

  deck.forEach(card => {
    point += card.cardPoint();
  })
  return point; //記得要return分數回去
}
```

2.一開始大家都是0點

```javascript
let yourPoint = 0;
let dearlerPoint = 0
```

3.呈現分數

```javascript
function renderGameTable() {
...
  yourPoint = calcPoint(yourDeck);
  dealerPoint = calcPoint(dealerDeck);
  
  $('.your-cards h1').html(`你 (${yourPoint}點) `); 
  $('.dealer-cards h1').html(`莊家 (${dealerPoint}點) `);  
}  
```

## J. 遊戲重置 `resetGame()`

1.把所有變數歸零

```javascript
function resetGame() {
  deck = [];
  yourDeck = [];
  dealerDeck = [];
  yourPoint = 0;
  dearlerPoint = 0;  
}
```

2.在啟動遊戲前先reset game

```javascript
// Part 3 啟動遊戲:
function newGame() {
  // 初始化遊戲
  resetGame();
  ...
}
```

# Part 2. 遊戲進行中

## Q. 利用`inGame` 變數，標示遊戲進行中

1.inGame 布林值

```javascript
inGame = true //當new game按下去之後，處於遊戲狀態中
```

2.在新牌局內，標示遊戲進行中

```javascript
function newGame() {
  resetGame();

  deck = shuffle(buildDeck());
  yourDeck.push(deal());
  dealerDeck.push(deal());
  yourDeck.push(deal());  

  //遊戲進行中
  inGame = true;

  //把玩家和莊家的卡牌畫出來
  renderGameTable();
}
```

## K. 設定另外兩個按鈕屬性 `.attr()`

1.按下`New Game`按鈕，啟動另外兩顆按鈕功能

```javascript
  if (inGame) {
    $('#action-hit').attr('disabled', false);
    $('#action-stand').attr('disabled', false);
  }
  else {
    $('#action-hit').attr('disabled', true);
    $('#action-stand').attr('disabled', true);    
  }
}

```

2.按鈕的打開與否，和inGame反向。

以上`if-else`可以簡寫為:
```javascript
    $('#action-hit').attr('disabled', !inGame);
    $('#action-stand').attr('disabled', !inGame);  
```

3.initButton() 設定另外兩顆按鈕的event

```javascript
function initButtons() {
  $('#action-new-game').click( evt => newGame() );

  //玩家叫牌
  $ ('#action-hit').click( evt => {
    evt.preventDefault();
    yourDeck.push(deal()); //從deal()移動一張牌推進去陣列
    renderGameTable(); //再畫一次  
  });

  //玩家停止叫牌，換莊家  
  $ ('#action-stand').click( evt => {
    evt.preventDefault();
    dealerDeck.push(deal()); //從deal()移動一張牌推進去陣列
    renderGameTable(); //再畫一次  
  });
}
```

## L. 遊戲結束的條件: 任何一方>=21點

```javascript
  //遊戲結束的條件
function renderGameTable() {
...
  if (yourPoint >= 21 || dearlerPoint >=21) { 
    inGame = false; //hit & stand按鈕關閉
  }
}  
```

## M. 點數精算

Ace牌可以等於1點或11點，調整`calc()`

```javascript
function calcPoint(deck) {
  let point = 0;

  deck.forEach(card => {
    point += card.cardPoint();
  })
  // M. 精算點數
  if (point > 21) {
    deck.forEach(card => {
      if (card.cardShowNumber() == 'A'){
        point -= 10; // A從11點變1點
      }
    })
  }
  return point;
}
```

## N. 莊家行動 `dealerRound()`

1.先把流程用中文寫下來

```javascript
  //1. 發牌
  //2. 如果點數 >= 玩家，結束，莊家贏
  //3. 如果 < 玩家，重複1
  //4. >21點，結束，玩家
```

2.使用while迴圈判斷

```javascript
function dealerRound() {

  while(true) { //whie: 不知道要做幾次, 無窮迴圈
    dealerPoint = calcPoint(dealerDeck);
    if (dealerPoint < yourDeck) {
      dealerDeck.push(deal());
    } else {
      break;
    } 
  }
  inGame = false; //跳出後，結束遊戲
  renderGameTable();
}
```

3.以上由`dealerRound()`在`stand`的button區塊呼叫

```javascript
function initButtons() {
...
  $ ('#action-stand').click( evt => {
    evt.preventDefault();
    dealerDeck.push(deal()); //再從deal()移動一張牌推進去陣列
    //renderGameTable(); 
    //因為dealerRound已經再畫一次，此行註解掉
    dealerRound();
  });
}
```

# Part 3. 遊戲結束，判斷贏家

1.設定贏家變數

```javascript
let winner = 0; // 0: 未定, 1: 玩家贏 2: 莊家贏 3: 平手
```

2.贏家重置

```javascript
function resetGame() {
  deck = [];
  yourDeck = [];
  dealerDeck = [];
  yourPoint = 0;
  dearlerPoint = 0;  
  winner = 0; //不要忘記這段!
}
```

3.畫牌桌的時候，就把誰贏誰輸顯示出來

```javascript
function renderGameTable() {
...
  switch(true){
    // 0. 比點數大小
    case dealerPoint > yourPoint:
      winner = 2;
      break;

    // 1. 如果玩家21點，玩家贏
    case yourPoint == 21:
      winner = 1;
      break;
    case dearlerPoint == 21:
      winner = 2;
      break;      
    
    // 2. 如果玩家點數爆
    case yourPoint > 21:
      winner = 2;
      break;
    
    // 3. 如果莊家點數爆
    case dealerPoint > 21:
      winner = 1;
      break;
    
    // 4. 平手
    case yourPoint == dealerPoint:
      winner = 3;
      break;
      
    // 5. 最後再用莊家的點來比點數
    case dealerPoint > yourPoint:
      winner = 2;
      break;        

    default:
      winner = 0;
      break;
  }
}  
```

4. 蓋勝利章

```javascript
  switch(winner) {
    case 1:
      $('.your-cards').addClass('win');
      break;

    case 2:
      $('.dealer-cards').addClass('win');
      break; 
    
    case 3: //平手
      break;

    default:
      break;
  }
```

# Part 4. 整理程式碼

O. 將`renderGameTable()`拆成小功能

```javascript=
  checkWinner();
  showWinStamp();
```

# HTML + CSS 程式結構

html  
抽到的牌會放在`<span></span>`區塊

莊家牌組

```html
<section class="dealer-cards zone">
    <h1>莊家</h1>
        <div class="deck">
          <div class="card"><span></span><div class="dealerCard" id="dealerCard1"></div></div>
          <div class="card"><span></span><div class="dealerCard" id="dealerCard2"></div></div>
          <div class="card"><span></span><div class="dealerCard" id="dealerCard3"></div></div>
          <div class="card"><span></span><div class="dealerCard" id="dealerCard4"></div></div>
          <div class="card"><span></span><div class="dealerCard" id="dealerCard5"></div></div>
        </div>
</section>
```

玩家牌組

```html
<section class="your-cards zone">
    <h1>你</h1>
        <div class="deck">
          <div class="card"><span></span><div class="yourCard" id="yourCard1"></div></div>
          <div class="card"><span></span><div class="yourCard" id="yourCard2"></div></div>
          <div class="card"><span></span><div class="yourCard" id="yourCard3"></div></div>
          <div class="card"><span></span><div class="yourCard" id="yourCard4"></div></div>
          <div class="card"><span></span><div class="yourCard" id="yourCard5"></div></div>
        </div>
</section>
```

按鈕區

```html
<footer class="action">
        <input type="button" id="action-hit" class="action-button" value="Hit!" disabled>
        <input type="button" id="action-stand" class="action-button" value="Stand" disabled>
        <input type="button" id="action-new-game" class="action-button" value="New Game">
      </footer>
```

CSS

分散對齊

```css
.deck {
  display: flex;
  justify-content: space-around;
}
```

勝利效果

```css
h1::after {
  position: absolute;
  border: solid 5px #d00;
  border-radius: 50%;
  color: #d00;
  content: '勝';
  font-size: 80px;
  font-weight: bold;
  padding: 10px;
  margin: 0;
  opacity: 0;
  transform: scale(5);
  transition: all 0.3s cubic-bezier(0.6, 0.04, 0.98, 0.336);
}

.win h1::after {
  opacity: .85;
  transform: rotate(-15deg) scale(1);
  z-index: 1000;
}
```

# 其他功能練習 

1. 新增一個Deck類別，讓Deck可以自行計算分數
2. bug: 避免起始的時候直接輸掉

# 流程圖 flowchart

利用Scalable Vector Graphics (SVG) 可縮放式向量圖檔製作流程圖。

JS Code Flowchart  
https://github.com/Bogdan-Lyashenko/js-code-to-svg-flowchart  
JS Code Flowchart Demo  
https://bogdan-lyashenko.github.io/js-code-to-svg-flowchart/docs/live-editor/  


# 附：撲克牌遊戲所需素材

撲克牌花色：  
♠♥♣♦  
圈圈字：  
㊀㊁㊂㊃㊄㊅㊆㊇㊈㊉㊊㊋㊌㊍㊎㊏㊐㊑㊒㊓㊔㊕㊖㊗㊘㊙㊚㊛㊜㊝㊞㊟㊠㊡㊢㊣㊤㊥㊦㊧㊨㊩㊫㊬㊭㊮㊯㊰

# 相關連結

[HackMD筆記](https://hackmd.io/b0Bfp09RRZidfpoF3Ad9Mg?view)  
[CodePen練習](https://codepen.io/tingtinghsu/full/ZZQewG)  
[網頁版完成品](../public/note/jsdemo/24-blackjack.html){:target"_blank"}  
[google font](https://fonts.googleapis.com/css?family=Open+Sans+Condensed:300)  
[陣列的洗牌(shuffle)功能](https://stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array)  
