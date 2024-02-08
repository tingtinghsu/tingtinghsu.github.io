---
title:  "Github介紹與Git Flow 開發流程"
preview: "Git senario"
permalink: "/articles/2019-05-06-git_flow_Github"
date:   2019-05-06 09:56:00
layout: post
tags:
  - "note"  
  - "git"
comments: true
---


Git Flow: 建立團隊專屬流程，成員訂下並遵守`commit`的規則。
![Git Flow: 團隊專屬流程](https://i.imgur.com/oFbBeZe.png)


<!-- more -->

1. `master`: 正式上線版本
2. `develop`: 開發版本
3. `feature`: 各自的功能，完成後合併至`develop`
4. `release`: 
    * 從`develop`分支出來，如同準備上線前的beta測試版。
    * `release`修改好後分別合併至master和develop各一份
6. `hotfix`: 從`master`分支出來，需要馬上修改好的bug，修改後合併至master和develop。

* 依照團隊專案需求建立適當branch，不一定全照著上述的branch規劃開發。(圖片ref: [I'm coding it線上課程](https://iamcoding.tw/))


---
目錄:
* abstact
{:toc}

---

設定GitFlow時我使用的是GitHub，因此我們來花點篇幅講解GitHub的設定方式。

# GitHub簡介

大部分的Git操作都是在電腦完成，若要跟別人共同協作，有Git伺服器會比較方便。

GitHub = 一款有Web介面的Git伺服器
(此外也有GitLab / BitBucket...等網站)

優點:  
1.跟世界各地厲害的開發者們交朋友  
2.開發者最好的履歷!  

# 開新專案

New repository，網頁版右上角 `+` 

## 1.全新專案

```bash
…or create a new repository on the command line
echo "# 5x-demo" >> README.md
git init
git add README.md
git commit -m "first commit"

git remote add origin https://github.com/tingtinghsu/5x-demo.git
git push -u origin master
```

可以選擇使用`https`(在推送的過程中會需要輸入帳號密碼)，或者是選擇`SSH`設定 (git[at]github.com:tingtinghsu/5x-demo.git)

## 2. 現成專案

### 方法一: 使用command line設定

```bash
/*設定一個遠端節點*/
git remote add origin /*origin節點名字可以自己取*/
https://github.com/tingtinghsu/5x-demo.git

/*把本機的master推到遠端*/
git push -u origin master
```

### 方法二: 在source tree設定

Repository -> Repository Settings... ->  
Remote name: origin  
URL/Path: https://github.com/tingtinghsu/5x-demo.git

```bash
tingdeAir:git-rebase tingtinghsu$ git push -u origin master
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 440 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/tingtinghsu/5x-demo.git
   647145b..6cc2c94  master -> master
```

設定完後，就可以讓本機的專案與遠端GitHub伺服器連結了！

# 跟遠端連接的git相關指令

## `git remote add origin` + 網址

* 新增一個叫做origin的遠端節點，這個節點會指向某個網址
* `origin`可以換為別的名稱

## `git push` origin master

* 要把本地的master分支，推往origin這個遠端節點，並且在遠端形成一個master分支
* 當遠端節點的版本比較新的情況，使用`git pull`或`git fetch`

## `git fetch` origin master

* 去`origin`這個遠端節點，抓上面`master`分支的內容，並且在我的電腦上建立一個`origin/master`分支


* CL

```bash
tingdeAir:git-rebase tingtinghsu$ git merge origin/master
Updating 6cc2c94..ea6fdcd
Fast-forward
 index.html | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

* 在source tree

在origin/master上按右鍵 -> `merge` ->  
Are you sure you want to merge `origin/master`   into your current branch?  
-> 確定  

* `master`的貼紙移動到`origin/master`
* 合併分支，就是在移動`branch`的貼紙

## `git pull` origin master

* 去`origin`這個遠端節點，抓上面`master`分支的內容，並且在電腦上建立一個`origin/master`分支
* 同時與本機的master分支進行合併

## `git pull` vs `git fetch`

git fetch: 跟remote比對，將local沒有的檔案拉下來 
git pull: `git fetch` + `git merge`

## `git clone` + 專案的Git網址

* 要把某個git網址的內容，整個複製一份到自己的電腦裡

```bash
tingdeAir:~ tingtinghsu
$ git clone https://github.com/tingtinghsu/learn-ruby-on-rails.git

Cloning into 'learn-ruby-on-rails'...
remote: Enumerating objects: 692, done.
remote: Total 692 (delta 0), reused 0 (delta 0), pack-reused 692
Receiving objects: 100% (692/692), 9.94 MiB | 1.76 MiB/s, done.
Resolving deltas: 100% (369/369), done.
Checking connectivity... done.
```

## 練習

```bash
1. 在Github上建立一個Repository
2. 把之前在本機上練習的檔案，推一份到這個新建的Repo裡
3. 直接在Github上對某個檔案進行編輯並存檔
4. 使用fetch或pull指令，把剛剛的更新抓下來
```

# 在Github 跟其他人一起工作

有時候同事更新比較快，會遇到推不上去的情形。

## Case: 同事A先push到master。當同事B要push到master的時候，推上不去

### 解法1: 使用 `git pull`

步驟:  
Step 1.`git pull --rebase`  

因為`git pull`包含了`git merge`功能，會讓兩者合併時產生新節點。

> 如果再加上`git pull --rebase`，會先把遠端的進度拉一份下來接在後面；再接自己本地的進度接在後面；看起來像是無縫接軌。

Step 2.`git push -f`  

`--force` 代表強行推送至git伺服器。因此使用此指令時一定要先知會團隊，讓大家以自己的版本為主。

### 解法2: `Pull Reqest`

`fork`一份專案到自己的帳號, 寫好後再送`Pull Reqest`

步驟: 

Step 1. 先`fork`專案到自己的帳號下  

> 這個解法更好的原因:  
1.開自己的分支撰寫程式，避免跟別人的版本打架  
2.不用每次push前先pull  

Step 2. 寫好後提出`pull request`


當專案擁有者收到PR後，點選`merge pull request`。  

> 選擇 `rebase and merge`選項，不會造成合併的節點，可以讓歷史記錄比較乾淨。

# Sourcetree的Git Flow標籤設定

## feature

1. 按介面右上角的`Git Flow`，建立`Start New Feature`
2. 新功能開發完後，`git commit`
3. 按介右上角的`Git Flow`，`Finish Feature`，合併到`develop`

## hotfix

當使用者發現網站功能有bug時, 會在GitHub上專案內建立`New issue`(開一張票)  
![](https://i.imgur.com/JE1FSSW.png)

開發者進行以下步驟:  
  
1.開發者在本機切換到`master`，`git pull`一份檔案  
2.按介面右上角的`Git Flow`，建立`Start New Hotfix`  
3.把票號(eg. `#2`)放在`Hotfix Name`  
4.修改bug後，`git commit`  
5.按介右上角的`Git Flow`，`Finish Hotfix`，合併各一份到`master`和`develop`，保持兩者同步  

![](https://i.imgur.com/jQbgGEj.png)



# 相關連結

[HackMD筆記: GitHub簡介](https://hackmd.io/X82JI7apT5mr6Gp3q4OnFg?view)  
[HackMD筆記: Git開發流程](https://hackmd.io/0b5JkRVPTSGVvAK9xmnMXw?view)  
[我的GitHub帳號](https://github.com/tingtinghsu)