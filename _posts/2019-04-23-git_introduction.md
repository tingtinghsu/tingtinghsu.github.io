---
title:  "[Git 簡介"
preview: "Git introduction"
permalink: "/articles/2019-04-23-git_introduction"
date:   2019-04-23 09:56:00
layout: post
tags:
  - "note"  
  - "git"
comments: true
---

Git是目前最多人使用的分散式版本控制系統，優點主要有三個:

1. 可讓檔案回到指定的時間點狀態
2. 可了解檔案內容如何變化
3. 可以知道每一行程式碼是誰寫的

<!-- more -->

Git的發明者: Linus Torvalds (也就是開發Linux的大神!)  


---
目錄:
* abstact
{:toc}

---

# 常用Terminal指令

iTerm2: https://www.iterm2.com/

Q: 爲什麼要學指令?
A: 有些指令在GUI(圖型介面軟體)找不到

**重點：要在對的地方（資料夾/目錄）下對的指令！**

## 印出目前工作資料夾: `pwd`  (print working directory)

```bash
tingdeAir:~ tingtinghsu$ pwd
/Users/tingtinghsu
```

## 新增資料夾：`mkdir` (make directory)

建立新的資料夾

```bash
tingdeAir:~ tingtinghsu$ mkdir hello
tingdeAir:~ tingtinghsu$ ls
5x		Desktop		GitHub		Pictures	projects
Applications	Documents	Library		Public
Books		Downloads	Movies		dwhelper
Calibre Library	Dropbox		Music		hello
```

## 改變資料夾目錄: `cd` + 資料夾名稱 (change directory)

```bash
tingdeAir:~ tingtinghsu$ cd hello/
tingdeAir:hello tingtinghsu$ pwd
/Users/tingtinghsu/hello
```

## 新增檔案：`touch`

建立檔案

```bash
tingdeAir:hello tingtinghsu$ touch index.html
tingdeAir:hello tingtinghsu$ ls
index.html
```

### 再touch一次同檔名的檔案：改變時間戳記

```bash
tingdeAir:hello tingtinghsu$ touch index.html
tingdeAir:hello tingtinghsu$ ls -al
total 0
drwxr-xr-x   3 tingtinghsu  staff    96  4 24 12:19 .
drwxr-xr-x+ 79 tingtinghsu  staff  2528  4 24 12:16 ..
-rw-r--r--   1 tingtinghsu  staff     0  4 24 17:19 index.html
```

## 回到上一層目錄 `cd ..`

```bash
tingdeAir:hello tingtinghsu$ pwd
/Users/tingtinghsu/hello
tingdeAir:hello tingtinghsu$ cd ..
tingdeAir:~ tingtinghsu$ pwd
/Users/tingtinghsu
tingdeAir:~ tingtinghsu$
```

## 列出資料夾下的所有檔案: `ls -al` (list all)

```bash
tingdeAir:hello tingtinghsu$ ls -al
total 0
drwxr-xr-x   3 tingtinghsu  staff    96  4 24 12:19 .
drwxr-xr-x+ 79 tingtinghsu  staff  2528  4 24 12:16 ..
-rw-r--r--   1 tingtinghsu  staff     0  4 24 12:19 index.html
```

## 刪除檔案: `rm` (remove)

刪除檔案

```bash
tingdeAir:hello tingtinghsu$ ls -al
total 0
drwxr-xr-x   4 tingtinghsu  staff   128  4 24 17:24 .
drwxr-xr-x+ 79 tingtinghsu  staff  2528  4 24 12:16 ..
-rw-r--r--   1 tingtinghsu  staff     0  4 24 17:24 images
-rw-r--r--   1 tingtinghsu  staff     0  4 24 17:19 index.html
tingdeAir:hello tingtinghsu$ rm index.html
tingdeAir:hello tingtinghsu$ ls
images
```

## 刪除資料夾: `rm -r`

刪除資料夾

```bash
tingdeAir:hello tingtinghsu$ rm -r images
tingdeAir:hello tingtinghsu$ ls

```

## 複製: `cp` (copy)

複製檔案並改檔名

```bash
tingdeAir:hello tingtinghsu$ cp hello.html world.html
tingdeAir:hello tingtinghsu$ ls
hello.html	world.html
```

## 移動檔案/更改檔名: `mv` (move)

1. 移動檔案到另一個資料夾

```bash
tingdeAir:hello tingtinghsu$ mkdir images
tingdeAir:hello tingtinghsu$ ls
hello.html	images		world.html

tingdeAir:hello tingtinghsu$ mv world.html images

tingdeAir:hello tingtinghsu$ ls
hello.html	images
tingdeAir:hello tingtinghsu$ cd images/
tingdeAir:images tingtinghsu$ ls
world.html
```

2. 更改檔名

```bash
tingdeAir:images tingtinghsu$ mv world.html pics.html
tingdeAir:images tingtinghsu$ ls
pics.html
```

# git安裝

網址: https://git-scm.com/

git 的GUI軟體(Graphical User Interface, 圖形使用者介面)
https://www.sourcetreeapp.com/

## git `--version`

```bash
tingdeAir:~ tingtinghsu$ git --version
git version 2.6.3
```

# git設定

## 檢視基本資料: `git config --list`

```bash
tingdeAir:images tingtinghsu$ git config --list
user.name=tingtinghsu
user.email=tingtinghsu[at]gmail

```

設定基本資料  
`git config --global` user.name  
`git config --global` user.email  

## 初始化: `git init`

```bash
tingdeAir:git-demo tingtinghsu$ git init
Initialized empty Git repository in /Users/tingtinghsu/git-demo/.git/

tingdeAir:git-demo tingtinghsu$ ls -al
total 0
drwxr-xr-x   5 tingtinghsu  staff   160  4 24 18:45 .
drwxr-xr-x+ 79 tingtinghsu  staff  2528  4 24 18:43 ..
drwxr-xr-x   9 tingtinghsu  staff   288  4 24 18:45 .git
-rw-r--r--   1 tingtinghsu  staff     0  4 24 17:31 hello.html
drwxr-xr-x   3 tingtinghsu  staff    96  4 24 18:29 images
```

# git 狀態

## 查詢 Git 狀態: `git status`

```bash
tingdeAir:git-demo tingtinghsu$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	hello.html
	images/
```

## git 的三個狀態

1. working 工作目錄

### `git add`: working到staging

```bash
tingdeAir:git-demo tingtinghsu$ git add hello.html
tingdeAir:git-demo tingtinghsu$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   hello.html
```

2. staging 暫存區域

### `git commit` `-m` : staging到repository

```bash
tingdeAir:git-demo tingtinghsu$ git commit -m "add html"
[master (root-commit) 99a855b] add html
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 hello.html
```

Q: 為什麼要git add/git commit 兩段式?  
A: 就像堆了一批貨再進倉庫一樣，要留下有意義的註記。

3. repository 儲存庫

### `git push`: repository 本地到遠端
### `git pull`: repository 遠端到本地

# 檢視 git 記錄: `git log`

列出最近commit過的記錄

```bash
tingdeAir:git-demo tingtinghsu$ git log
commit 715717d24bd8250245ff8de446fb797887ac429c
Author: tingtinghsu <tingtinghsu.tw@gmail.com>
Date:   Wed Apr 24 21:41:32 2019 +0900

    add image file

commit 99a855baae705635f825178c29b4a97979e754fa
Author: tingtinghsu <tingtinghsu.tw@gmail.com>
Date:   Wed Apr 24 21:35:07 2019 +0900

    add html
```

## `git log` `--oneline` : 列出一行記錄

```bash
tingdeAir:git-demo tingtinghsu$ git log --oneline
715717d add image file
99a855b add html
```

# 相關連結

[HackMD筆記](https://hackmd.io/O-kGxlQkQdOBefjHH9vfWQ?view)  
[我的GitHub帳號](https://github.com/tingtinghsu)