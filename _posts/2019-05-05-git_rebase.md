---
title:  "Git rebase 修改commit歷史記錄"
preview: "Git rebase"
permalink: "/articles/2019-05-05-git_rebase"
date:   2019-05-05 09:56:00
layout: post
tags:
  - "note"  
  - "git"
comments: true
---

雖然commit就像承諾一樣一諾千金，但是遇到要修改的情形該怎麼辦才好？今天我們要來介紹一個可以修改commit的指令: `git rebase`。

<!-- more -->

---
目錄:
* abstact
{:toc}

---

# 1. 修改commit 訊息 `git rebase -i`

git rebase 使用互動模式

## 解法一 (source tree)
要修改的commit，找到其init commit按右鍵 ->  
Rebase children of `bb0c9c2` interactively... ->  
點選想修改的commit, 按 `Edit Message` -> OK  

結果: 造成蝴蝶效應  
修改的那一點commit, 和其 children commit的index全部改變

## 解法二 (CL)

指令: `git rebase -i bb0c9c2` (C1的index)
意思: 修改從C6到C1的歷史訊息！
1. 修改了C2的Commit訊息
2. 因而產生新的歷史記錄C3~C6
3. 分支及HEAD移到新的C6
4. 原C3~C6脫離
5. 新C3~C6看起來一樣，卻是不同的commit (平行時空的兩條分支)

```
pick 7de7617 add pig
pick a252c2a add fish

# Rebase 1b8128c..a252c2a onto 1b8128c (2 command(s))
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
~                                                                               
~                                                                               
"~/git-examples2/git-rebase/.git/rebase-merge/git-rebase-todo" 21L, 684C
```

# 2. `刪除`commit或`調整`commit的順序

任務:  
想把cute animals移到pig的後面  

## 解法 (source tree)

要修改的commit，找到其init commit按右鍵 ->  
Rebase children of `bb0c9c2` interactively... ->  
`拖拉`或是`點選上下`

children commit的index受到影響

# 3. 把多個commit合併成一個 `squash`

同一組功能的修改commit一次就好

## 解法 (source tree)

要修改的commit，找到其parent commit按右鍵 ->  
Rebase children of `bb0c9c2` interactively... ->  

把`add cat1`, `add cat2`, `add 2 cats` 
3個commit squash在一起

`Squash with previous`

把cat訊息改成`add all cats`  

```bash
add 2 cats (+2 squashed commits)
Squashed commits:
[1de2076] add cat 2
[cd82f29] add cat 1
```

把dog訊息`add all dogs` 較簡潔

```bash
add dog 2 (+1 squashed commit)
Squashed commits:
[0e131b6] add dog 1
```

# 4. 把一個commit拆成多個 

## 1. 先使用 `reset -i`，移動HEAD

要修改的commit，找到其init commit按右鍵 ->  
Rebase children of `bb0c9c2` interactively... ->  

勾選`add all cats`的`Amend Commit` ->  

* `HEAD`已經移到 `add database settings`  
* 提醒git在rebase過程中，先在這個commit停下來  

## 2. 接下來的步驟用 `reset`

點選上一顆parent commit按右鍵 ->  
提醒在rebase過程中，先在這個commit停下來  
Reset current branch to this commit ->  
Reset to Commit...  
Using mode: Mixed - Keep working copy but reset index

* 重點: 此時是reset --mix (很多檔案都回到Uncommit的狀態)
* 把4個檔案丟回工作目錄，再兩兩合併)

 按 `add database settings` 檢查當時的狀態
 總共有 cat1.html, cat2.html, cat3.html, cat4.html  
 四個檔案，兩兩合併commit  

```
 cat1.html, cat2.html => commit -m "add cat1 & cat2"  
 cat3.html, cat4.html => commit -m "add cat3 & cat4"  
``` 

 * `HEAD`已經移到 `add cat3 & cat4`
 
 ## 3.最後，Actions -> Continue Rebase
 
 rebase 還沒完成，只是在中間的過程先調整  
 告訴後面的branch可以接回來了  
 
 這個小節比較複雜，加上我的sourcetree是日文版（為了學習語文的目的），所以試了好多次。最後終於成功了！！開心 :D

# 相關連結

[HackMD筆記](https://hackmd.io/UdERZlR2QhiUrOj4QM_EEQ?view)  