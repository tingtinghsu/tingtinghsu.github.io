---
title:  "Git reset 與 Git tag 標籤 : 回到過去的commit"
preview: "Git reset Git tag"
permalink: "/articles/2019-05-04-git_reset_tag"
date:   2019-05-04 09:56:00
layout: post
tags:
  - "note"  
  - "git"
comments: true
---

有時候我們想回到某個分支上的commit的狀態去修改檔案，`git reset`就是非常好用的指令！

<!-- more -->

---
目錄:
* abstact
{:toc}

---

# `git reset` 的使用情境
  
若我們想回到`master`分支上前一個commit的狀態  
  
此時可以輸入: `git reset 85e7e30`  
  
![commit記錄](https://i.imgur.com/Z3z98rQ.png)  
  
1. 目前的分支以及HEAD移動到上一次commit
2. Commit`e12d8ef`暫時看不見了 （而不是砍掉）
3. 原本Commit的內容，會被放至工作目錄（預設行為）

# `git reset` `index` `--參數` 決定檔案去留
  
reset後面接三種參數:  
  
`-- hard`  工作目錄和暫存區的內容都丟掉（直接丟掉）  
`-- soft`  工作目錄和暫存區的內容都不變（檔案丟回暫存區）`(預設)`  
`--mixed` 工作目錄不變，暫存區內容丟掉（檔案丟回工作目錄）  

## hard
`-- hard`工作目錄和暫存區的內容都丟掉（直接丟掉）

指令: `git reset5 85e7e30 --hard`

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git reset 85e7e30 --hard
HEAD is now at 85e7e30 add hello

tingdeAir:git-branch1 tingtinghsu$ git log --oneline
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
nothing to commit, working directory clean
```

### 重點1. 復原`--hard`裡被丟掉的工作目錄和暫存區內容

#### A. 絕對定位 `index`

(1) command line

指令: `git reset e12d8ef --hard`

原本的最後一次commit: `e12d8ef`

意義：
1. 目前的分支以及HEAD移動到C5 (最後一次的commit)
2. 整個專案變成C5時候的樣子 (所有commit的資料都回來了)

```bash
tingdeAir:git-branch1 tingtinghsu$ git reset e12d8ef --hard
HEAD is now at e12d8ef add database.yml in config folder

e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
````

(2) GUI軟體: source tree
在想要復原的commit上按右鍵  
-> `Reset current branch to this commit`  
-> `Using mode:`  

1. Mixed - keep working copy but reset index  
2. Soft - keep all local changes  
3. Hard - discard all working copy changes  

#### B. 相對定位 `^` `~`

想回到某個commit的上一個、上兩個commit，可以使用這兩種符號做相對定位:

`^` = Caret (卡瑞特)
`~` = Tilde (提爾達)

* `^` Caret

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git reset e12d8ef^
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git reset 85e7e30^^
Unstaged changes after reset:
M	index.html
tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   index.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	config/
	hello.html
```

* `~` Tilde
用HEAD當代名詞
`git reset HEAD~2` : 我想要變成HEAD的前2個狀態！

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git reset HEAD~2
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

### 重點2. 查詢不見的commit: `git reflog`

reflog = reference log
列出HEAD移動的軌跡圖

`git log`: 列出commit記錄
```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git reset HEAD~2
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```
`git relog`: 列出HEAD在不同Commit移動的紀錄
```bash
tingdeAir:git-branch1 tingtinghsu$ git reflog
85e7e30 HEAD@{0}: reset: moving to 85e7e30
e12d8ef HEAD@{1}: reset: moving to e12d8ef
657fce7 HEAD@{2}: reset: moving to 657fce7
85e7e30 HEAD@{3}: reset: moving to 85e7e30

/*回到原來的狀態*/
tingdeAir:git-branch1 tingtinghsu$ git reset e12d8ef --hard
HEAD is now at e12d8ef add database.yml in config folder

tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
nothing to commit, working directory clean

tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

## soft

`-- soft`  工作目錄和暫存區的內容都不變（檔案丟回暫存區）

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git reset 657fce7 --soft

tingdeAir:git-branch1 tingtinghsu$ git log --oneline
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.html
```

## mixed (預設)

`--mixed` 工作目錄不變，暫存區內容丟掉（檔案丟回工作目錄）

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
nothing to commit, working directory clean
tingdeAir:git-branch1 tingtinghsu$ git reset 85e7e30
tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	config/
```

## `git reset`和`git checkout`的差異

checkout: 去其他的commit看看

1. 分支不動，HEAD移動到其他的commit
2. 專案變成當時commit的狀態（如果原本是乾淨狀態的話）
3. checkout不像reset指令有相關參數可以設定檔案去留

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git checkout 657fce7
Note: checking out '657fce7'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 657fce7... add container

tingdeAir:git-branch1 tingtinghsu$ git log --oneline
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

# tag的使用情境

設定更新或上線的里程碑 (產品的版本號)

`git tag 1.0.0 cef6e40`

![在commit上設定tag](https://i.imgur.com/9Yrmwaq.png)

# `git tag` + `版本號`

Q: 為什麼要用標籤設定版本號?  
A: 不想要記得commit index的長串文數字

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git tag 1.0.0 cef6e40
```

## tag 用於 `git reset`

回到過去的版本號(做修改)

```bash
tingdeAir:git-branch1 tingtinghsu$ git reset 1.0.0 --hard
HEAD is now at cef6e40 create index page
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
cef6e40 create index page
cc797cd init commit
```

## tag 用於 `git checkout`

回到過去的版本號看一看(look around)

```bash
tingdeAir:git-branch1 tingtinghsu$ git checkout 1.0.0
Note: checking out '1.0.0'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at cef6e40... create index page
tingdeAir:git-branch1 tingtinghsu$ git branch
* (HEAD detached at 1.0.0)
  master
tingdeAir:
```

## `git tag` 和 `git branch` 的差異

`標籤`和`分支`有什麼不同? (參考上一篇的[Git branch筆記](http://127.0.0.1:4000/blog/articles/2019-04-25-git_branch)複習)

1. tag 標籤貼上去之後，會一直停在同一個commit
2. branch 分支會跟著commit的推進而延伸長大

> 留下來(tag)，或我跟你走(branch)！


# 相關連結

[HackMD筆記: Git reset](https://hackmd.io/fOylk_kCRpS64GKNzn-oJg?view)  
[HackMD筆記: Git tag](https://hackmd.io/q2uZMBf2SO-5tmpVi-pNUg?view)  

