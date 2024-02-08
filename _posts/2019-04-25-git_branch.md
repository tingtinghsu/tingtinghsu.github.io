---
title:  "Git branch 分支"
preview: "Git branch"
permalink: "/articles/2019-04-25-git_branch"
date:   2019-04-25 09:56:00
layout: post
tags:
  - "note"  
  - "git"
comments: true
---

在Git建立分支`branch`的好處，就是可以讓我們在不同分支實作不同的功能，最後再使用`merge`把開發功能的分支合併到主分支`master`。

<!-- more -->

建立分支的概念，如同跟幫你新作的功能/檔案`貼上分類的貼紙`。

---
目錄:
* abstact
{:toc}

---

# Examle 1 直線進行的分支

## 1. `git branch` 列出所有分支 

```bash
/Users/tingtinghsu/git-examples/git-branch1
tingdeAir:git-branch1 tingtinghsu$ git branch
* master
```

## 2. `git branch` `cat` 建立新分支

```bash
tingdeAir:git-branch1 tingtinghsu$ git branch
  cat /* 貼貼紙 */
* master
```

## 3. `git checkout` `cat` 切換到cat分支

`HEAD`: 上帝視角

1. HEAD移動到cat貼紙，並且跟著cat
2. 專案會變成cat貼紙指到的Commit狀態
3. 目前cat跟master所在位置一樣


```bash
tingdeAir:git-branch1 tingtinghsu$ git checkout cat
Switched to branch 'cat'
tingdeAir:git-branch1 tingtinghsu$ git branch
* cat
  master
```

### 3-1 在`cat` branch送commit

使用checkout之後送commit

1.cat貼紙與HEAD往前移動(如下圖，`cat`分支變為粗體字)
2.其他分支留在原地

![圖:cat貼紙與HEAD往前移動](https://i.imgur.com/auRG8fF.png)

```bash
tingdeAir:git-branch1 tingtinghsu$ git branch
* cat
  master

tingdeAir:git-branch1 tingtinghsu$ touch .keep
tingdeAir:git-branch1 tingtinghsu$ git status
On branch cat
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.keep
    
tingdeAir:git-branch1 tingtinghsu$ git add .
tingdeAir:git-branch1 tingtinghsu$ git commit -m "add cat branch"
[cat 8fded81] add cat branch
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .keep
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
8fded81 add cat branch
865f98a add hello-world file
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

### 3-2 先切回`master`，再`git merge` `cat` 合併分支

跟master很像的分支，合併時會使用fast-forward快轉  
（git圖示上master往前走，沒有分支的小耳朵）

1.切換回master分支 `git checkout master`  
    - HEAD移動到master貼紙  
    - 專案會變成master貼紙指到的commit狀態  
     (如下圖，`master`分支變回粗體字)

![圖:專案會變成master貼紙指到的commit狀態](https://i.imgur.com/K1nTCbo.png)

2.合併cat分支 `git merge cat`  
    - master與HEAD移動到cat貼紙的所在地  
    - 快轉(Fast-forward)合併  

=> 合併分支可以看成在移動貼紙  
(如下圖，`master`貼紙移動到與`cat`貼紙相同的commit)

![master貼紙移動到與cat貼紙相同的commit](https://i.imgur.com/b9tmAjn.png)


```bash
tingdeAir:git-branch1 tingtinghsu$ git branch
* cat
  master
  
tingdeAir:git-branch1 tingtinghsu$ git checkout master
Switched to branch 'master'

tingdeAir:git-branch1 tingtinghsu$ git merge cat
Updating 865f98a..8fded81
Fast-forward
 .keep | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .keep

tingdeAir:git-branch1 tingtinghsu$ git log --oneline
8fded81 add cat branch
865f98a add hello-world file
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

## 4. `git branch -d`刪掉已合併的分支

1. 把cat貼紙撕起來
2. Commit 或是檔案不會因此受到影響

=> **Git 的本體是commit, 分支只是浮雲**

```bash
tingdeAir:git-branch1 tingtinghsu$ git branch -d cat
Deleted branch cat (was 8fded81).
```

# Examle 2 複雜一點的分支

分支的進度超過`master`分支，使得git圖形長得像y型merge

## 1. `git branch` `dog` 建立新分支

在目前HEAD所在地貼dog貼紙
 
```bash
tingdeAir:git-branch1 tingtinghsu$ git branch dog
tingdeAir:git-branch1 tingtinghsu$ git branch
  dog
* master
  rabbit
tingdeAir:git-branch1 tingtinghsu$
```

## 2. `git checkout` `dog` 切換git 到dog分支

1. HEAD移動到dog分支
2. 專案會變成dog貼紙指到的Commit狀態
3. 目前dog跟master所在位置一樣

```bash
tingdeAir:git-branch1 tingtinghsu$ git checkout dog
Switched to branch 'dog'
tingdeAir:git-branch1 tingtinghsu$ git branch
* dog
  master
  rabbit
```

### 2-1 在`dog` branch送commit

再進行一次存檔  
1.dog貼紙與HEAD往前移動  
2.其他分支貼紙留在原地  

HEAD像是令牌，只有領到令牌的分支才可以往前移動  

![dog貼紙與HEAD往前移動](https://i.imgur.com/c0XA4yv.png)

```bash
tingdeAir:git-branch1 tingtinghsu$ git commit -m "add dog 1"
[dog e560a0e] add dog 1
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .keepdog
```

### 2-2 在`dog` branch再送一次commit

```bash
tingdeAir:git-branch1 tingtinghsu$ git add .
tingdeAir:git-branch1 tingtinghsu$ git commit -m "add dog 2"
[dog 3f699df] add dog 2
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .keeptwodogs
```

第二個分支長太大，這時候的合併就沒辦法fast forward了

![分支長太大的合併就沒辦法fast forward](https://i.imgur.com/63w6piS.png)

### 2-3. `git merge` `rabbit` : 在dog分支合併rabbit分支

rabbit分支也commit過一次，若此時dog分支想要合併rabbit分支:  

1.長出一個合併的commit (dog和rabbit分支的公證人)
2.dog貼紙和HEAD往新的commit移動

![合併分支時產生小耳朵](https://i.imgur.com/fGz2BDC.png)

```bash
tingdeAir:git-branch1 tingtinghsu$ git checkout rabbit
Switched to branch 'rabbit'
tingdeAir:git-branch1 tingtinghsu$ ls
config		hello.html	index.html	welcome.html
tingdeAir:git-branch1 tingtinghsu$ touch rabbit
tingdeAir:git-branch1 tingtinghsu$ git add .
tingdeAir:git-branch1 tingtinghsu$ git commit -m "add rabbit"
[rabbit 6001538] add rabbit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 rabbit

tingdeAir:git-branch1 tingtinghsu$ git checkout dog
Switched to branch 'dog'
tingdeAir:git-branch1 tingtinghsu$ git merge rabbit
Merge made by the 'recursive' strategy.
 rabbit | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 rabbit
```

＊在Sourcetree打勾Checkout New Branch，代表建立分支之後立即切換過去該branch。

# Example 3 讓合併的節點消失：`git rebase`  

`git rebase`: 變換根基/嫁接

注意: 在git的世界裡，commit沒有辦法刪除，但透過`git rebase`可以將歷史commit記錄變成直線。

現在有`dog`與`bird`分支，分別進行過一次commit:  

## 切換到`dog`分支，HEAD移動到dog

![dog分支與bird分支](https://i.imgur.com/qxDoNGf.png)

## `git rebase` `bird`: 讓dog踩在bird分支的頭上

1. 複製一份dog的commit接到bird的commit後面，形成新的commit
2. HEAD移動到新的commit
3. dog貼紙移動到新的commit, HEAD指向bird貼紙
4. bird貼紙從舊的分支commit脫離  
（如下圖：bird分支已消失，歷史紀錄變成直線）

![bird分支的舊commit脫離](https://i.imgur.com/cPwVtqr.png)

```bash
tingdeAir:git-branch1 tingtinghsu$ git rebase bird
First, rewinding head to replay your work on top of it...
Applying: add dog3
```

# merge和rebase的使用時機

rebase
優點:  
拿來整理過多節點的時候，把節點拉成同條  

缺點:  
看不太出來歷史軌跡  

# 相關連結

[HackMD筆記](https://hackmd.io/ZSDGoCUaQFSBn9a1zYuURw?view)  
