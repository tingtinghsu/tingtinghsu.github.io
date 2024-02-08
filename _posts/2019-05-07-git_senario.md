---
title:  "Git 小劇場"
preview: "Git senario"
permalink: "/articles/2019-05-07-git_senario"
date:   2019-05-07 09:56:00
layout: post
tags:
  - "note"  
  - "git"
comments: true
---

本篇的Git 小劇場，將會模擬在專案開發實務上，檔案常發生各種狀況及問題。一起來進入這些情境並動動腦吧！

<!-- more -->

---
目錄:
* abstact
{:toc}

---

# part I. commit之前...

現有專案如下:

```bash
tingdeAir:git-examples tingtinghsu$ cd git-branch1
tingdeAir:git-branch1 tingtinghsu$ ls
config		hello.html	index.html	welcome.html

tingdeAir:git-examples tingtinghsu$ cd git-branch1/
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

## Case 1. 不小心刪掉檔案

```bash
tingdeAir:git-branch1 tingtinghsu$ rm *.html
tingdeAir:git-branch1 tingtinghsu$ ls
config
```

Step1. 先用 `git status`查發生什麼事
三個檔案被刪掉

```bash
tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    hello.html
	deleted:    index.html
	deleted:    welcome.html
```
Step2. 用`git checkout`把被刪掉的檔案登出staging area

如果是` git checkout .`：在staging area把所有變更恢復成最後一次存檔的狀態

```bash
tingdeAir:git-branch1 tingtinghsu$ git checkout index.html
tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    hello.html
	deleted:    welcome.html

```

被刪除的檔案就會再次出現在該資料夾  

```
no changes added to commit (use "git add" and/or "git commit -a")
tingdeAir:git-branch1 tingtinghsu$ ls -al
total 8
drwxr-xr-x@  5 tingtinghsu  staff  160  4 24 22:12 .
drwxr-xr-x@  9 tingtinghsu  staff  288  4 24 22:06 ..
drwxr-xr-x@ 18 tingtinghsu  staff  576  4 24 22:12 .git
drwxr-xr-x@  3 tingtinghsu  staff   96  8 20  2017 config
-rw-r--r--   1 tingtinghsu  staff  161  4 24 22:12 index.html
```

## Case 2. 看某行程式碼是誰寫的: 使用`git blame`

```bash
tingdeAir:git-branch1 tingtinghsu$ git blame index.html
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800  1) <!DOCTYPE html>
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800  2) <html>
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800  3)   <head>
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800  4)     <meta charset="utf-8">
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800  5)     <title>首頁</title>
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800  6)   </head>
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800  7)   <body>
657fce78 (Eddie Kao 2017-08-02 16:53:43 +0800  8)     <div class="container">
657fce78 (Eddie Kao 2017-08-02 16:53:43 +0800  9)     </div>
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800 10)   </body>
abb4f438 (Eddie Kao 2017-08-02 16:49:49 +0800 11) </html>
```

## Case 3. 新增目錄: 空目錄無法進版控

如果僅新增目錄後輸入`git status`, 出現nothing to commit
因為更新與否，是使用檔案內容做計算。

```bash
tingdeAir:git-branch1 tingtinghsu$ mkdir hello-world
tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
nothing to commit, working directory clean
```

慣例: 可新增一個`.keep`檔案到資料夾
```bash
tingdeAir:git-branch1 tingtinghsu$ touch hello-world/.keep
tingdeAir:git-branch1 tingtinghsu$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	hello-world/
```

# Part II. commit之後...

## Case 1. 剛剛做了一次commit，後悔了想拆掉重做

* 預設情境

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ ls
config		hello.html	index.html	welcome.html

tingdeAir:git-branch1 tingtinghsu$ touch dog.html
tingdeAir:git-branch1 tingtinghsu$ git add dog.html 
tingdeAir:git-branch1 tingtinghsu$ git commit -m "add dog"
[detached HEAD 3595e26] add dog
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 dog.html
```

### 解法: 使用`git reset`

解法一: 絕對位置`git reset e12d8ef`

```bash
tingdeAir:git-branch1 tingtinghsu$ git reset e12d8ef
tingdeAir:git-branch1 tingtinghsu$ git status
HEAD detached at e12d8ef
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	dog.html
```

解法二: 相對位置 `git reset HEAD^`

```bash
tingdeAir:git-branch1 tingtinghsu$ git reset HEAD^
tingdeAir:git-branch1 tingtinghsu$ git status
HEAD detached at e12d8ef
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	dog.html
    
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

GUI 
在想復原的前一顆commit:  
-> Reset current branch to this commit  
-> Using mode:  
Mixed - keep working copy but reset index
(保留檔案)  

## Case 2. 合併時發生衝突了，怎麼辦？

* 預設情境

`member`分支想合併`payment`分支

```bash
tingdeAir:git-examples2 tingtinghsu$ cd git-conflict/
tingdeAir:git-conflict tingtinghsu$ ls
config		hello.html	index.html	payment.html	welcome.html

tingdeAir:git-conflict tingtinghsu$ git merge member
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

### 解法: 修改merge後顯示為conflict的檔案，再add. commit

解法一: (CL)

1.`git status`觀察情況

```bash
tingdeAir:git-conflict tingtinghsu$ git status
On branch payment
You have unmerged paths.
  (fix conflicts and run "git commit")

Changes to be committed:

	new file:   member.html

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   index.html
```

2.打開VS code看被標註為C的檔案:

`<<<<<<<`: 原本的檔案 (current change)  
`>>>>>>>`: 預計合併的檔案 (incoming change)  

```html
    <div class="container">
<<<<<<< HEAD
      <a href="payment.html">金流系統</a>
=======
      <a href="member.html">會員系統</a>
>>>>>>> member
    </div>
```

3.把標注的符號清掉 `接受兩者變更`

```bash
    <div class="container">
      <a href="payment.html">金流系統</a>
      <a href="member.html">會員系統</a>
    </div>
```

4.回到CL確認狀態`git status`

`index.html` 檔案仍在工作目錄

```bash
tingdeAir:git-conflict tingtinghsu$ git status
On branch payment
You have unmerged paths.
  (fix conflicts and run "git commit")

Changes to be committed:

	new file:   member.html

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   index.html

```

5.再次commit

```bash
tingdeAir:git-conflict tingtinghsu$ git add .
tingdeAir:git-conflict tingtinghsu$ git commit -m "merge payment and member"
[payment 9353b45] merge payment and member
tingdeAir:git-conflict tingtinghsu$ git log --oneline

9353b45 merge payment and member
f3d9d77 update index page
9f28e3b add payment page
d818770 update links on index page
76940ed add member sign in page
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

解法二: (Source Tree) 

合併member分支到payment分支
在member上點右鍵 -> merge 

提示窗出現`conflict！`提醒要修改

點選預計合併的灰點
驚嘆號「！」檔案按右鍵: Resolve Conflict 
再完成add -> commit的步驟

## Case 3. 不小心把還沒合併的分支砍掉了，如何救？

* 預設情境

```bash
tingdeAir:git-branch2 tingtinghsu$ git branch
  cat
  dog
* master
```

站在master刪除dog  
訊息顯示: `還沒合併，是否刪除分支?`

```bash
tingdeAir:git-branch2 tingtinghsu$ git branch -d dog
error: The branch 'dog' is not fully merged.
If you are sure you want to delete it, run 'git branch -D dog'.
```

確認刪除分支，用`-D`大D做參數

```bash
tingdeAir:git-branch2 tingtinghsu$ git branch -D dog
Deleted branch dog (was 053fb21).
```

### 解法: 找回分支`index`重新設定`branch`名稱，分支就會再長回來

只要把dog貼紙貼回去，dog分支的commit就會再度有人看管

解法一: (CL)

1.列出所有HEAD移動過的軌跡，找到之前commit的index

```bash
tingdeAir:git-branch2 tingtinghsu$ git reflog
e12d8ef HEAD@{0}: checkout: moving from dog to master
053fb21 HEAD@{1}: checkout: moving from master to dog
e12d8ef HEAD@{2}: checkout: moving from cat to master
```

按下`q`離開

2.再貼一次貼紙

```bash
tingdeAir:git-branch2 tingtinghsu$ git branch new_dog 053fb21
tingdeAir:git-branch2 tingtinghsu$ git branch
  cat
* master
  new_dog

```

解法二: (Source Tree)

Sourcetree頁面上方 `Branch`->`New Branch` 命名new_dog
specified commit: `053fb21`

出現新分支

```bash
tingdeAir:git-branch2 tingtinghsu$ git branch
  cat
  master
* new_dog
```

若要再度刪掉`new_dog`分支，需切換分支才能刪除

```bash
tingdeAir:git-branch2 tingtinghsu$ git checkout master
Switched to branch 'master'
tingdeAir:git-branch2 tingtinghsu$ git branch -D new_dog
Deleted branch new_dog (was 053fb21).
tingdeAir:git-branch2 tingtinghsu$ git branch
  cat
* master
```

注意: 若太久沒使用(1,2個月後), git的回收機制會把commit收掉

## Case 4. 回到過去的某個commit再做一個新的分支出來

* 預設情境

```bash
tingdeAir:git-branch1 tingtinghsu$ git log --oneline
af8df39 add dog CL
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit
```

### 解法: 建立新分支時設定`specified commit`

Sourcetree頁面上方 `Branch`->`New Branch` 命名st-branch
specified commit: `cef6e40`

出現新分支

```bash
tingdeAir:git-branch1 tingtinghsu$ git branch
  master
* st-branch
```

在新分支長新的commit

```bash
tingdeAir:git-branch1 tingtinghsu$ git commit -m "abc in st-branch"
[st-branch 122ee9e] abc in st-branch
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 abc.html
```

## Case 5. 不小心使用hard模式reset某個Commit, 如何救回來?

* 預設情境

檔案全部都不見了，只剩下`git init`

```bash
/*master*/
af8df39 add dog CL
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git reset cc797cd  --hard
HEAD is now at cc797cd init commit

tingdeAir:git-branch1 tingtinghsu$ git log --oneline
cc797cd init commit

```

### 解法: 使用`git reflog`

```bash
tingdeAir:git-branch1 tingtinghsu$ git reflog
cc797cd HEAD@{0}: reset: moving to cc797cd
af8df39 HEAD@{1}: reset: moving to af8df39
e12d8ef HEAD@{2}: reset: moving to e12d8ef
```

按`q`離開，然後`git reset` + 想回到某個時間點的`index`

```bash
tingdeAir:git-branch1 tingtinghsu$ git reset e12d8ef --hard
HEAD is now at e12d8ef add database.yml in config folder
tingdeAir:git-branch1 tingtinghsu$ ls
config		hello.html	index.html	welcome.html
```

## Case 6. 某分支的某些commit做的不錯想收進來，但不想合併整個分支

* 預設情境

```bash
tingdeAir:git-branch3 tingtinghsu$ git status
On branch master

nothing to commit, working directory clean

tingdeAir:git-branch3 tingtinghsu$ git log --oneline
e12d8ef add database.yml in config folder
85e7e30 add hello
657fce7 add container
abb4f43 update index page
cef6e40 create index page
cc797cd init commit

tingdeAir:git-branch3 tingtinghsu$ git branch
  cat
  dog
  fish
* master
```

切到fish分支

```bash
tingdeAir:git-branch3 tingtinghsu$ git checkout fish
Switched to branch 'fish'
tingdeAir:git-branch3 tingtinghsu$ ls
dolphin.html	hello.html	shark.html	whale.html
gold-fish.html	index.html	welcome.html
```

想在dog分支把fish分支的whale檔案包進來
但是不要shark, dolphin, gold fish檔案

### 解法: 使用`cherrypick`

cherrypick單字意思: 挑選對自己有利的  

```bash
tingdeAir:git-branch3 tingtinghsu$ git checkout dog
Already on 'dog'

tingdeAir:git-branch3 tingtinghsu$ ls
config		dog2.html	index.html	whale.html
dog1.html	hello.html	welcome.html

tingdeAir:git-branch3 tingtinghsu$ git checkout fish
Switched to branch 'fish'
tingdeAir:git-branch3 tingtinghsu$ ls
dolphin.html	hello.html	shark.html	whale.html
gold-fish.html	index.html	welcome.html
```

挑選完後，兩個分支都會有whale檔案。

# Part III. 專案協同合作時...

## Case 1 `git add .`完之後準備要commit，卻發現忘記開分支

### 解法: 新增`branch`後commit, 再刪掉原有的branch

1.在此分支加上新貼紙`fish`，`git branch fish`，並且趕快`git checkout fish`過去。
2.再進行一次`git add .`，`git commit`
3.在`fish`分支上，刪掉`master branch`(撕掉貼紙)

```bash
tingdeAir:git-rebase tingtinghsu$ touch fish1.html
tingdeAir:git-rebase tingtinghsu$ git add .

tingdeAir:git-rebase tingtinghsu$ git branch fish
tingdeAir:git-rebase tingtinghsu$ git checkout fish
tingdeAir:git-rebase tingtinghsu$ git add .
tingdeAir:git-rebase tingtinghsu$ git commit -m "add fish1"
[fish 7ae141a] add fish1
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 fish1.html
 
tingdeAir:git-rebase tingtinghsu$ git branch -d master

```

4. 回到前一狀態(尚未增加新檔案的狀態)，再度加上`master branch`(再貼上master貼紙)


## Case 2 比較機密的檔案不想放在Git裡給閒雜人等看到

### 解法: `.gitignore`

ignore https://github.com/github/gitignore

1.新增`.gitignore`

```bash
MacBook-Air:git-rebase tingtinghsu$ touch .gitignore
```

2.編輯`.gitignore`，加入黑名單

```bash
pig*
bird*
```

3. 加入黑名單的檔案不再進入版控中

```bash
MacBook-Air:git-rebase tingtinghsu$ touch pig.html
MacBook-Air:git-rebase tingtinghsu$ touch bird.html
MacBook-Air:git-rebase tingtinghsu$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.gitignore
```

限制: 
之前已經產生的檔案，就算加入了`.gitignore`，還是會被git記錄到。必須先刪除再重新產生一份同名檔案。

## Case 3. 手邊的事情做一半，被老闆叫去修別的bug: `git stash`

可能的解法: 切換到master分支，做完再切回來

```bash
git commit
git checkout master
...(做別的步驟)
git checkout cat
git reset HEAD^ --mix
```

### 更好的解法: `git stash`  

1.`git stash` 使用git 中斷指令  

（也是一種commit，只是不會出現在歷史紀錄上）

```bash
tingdeAir:git-stash tingtinghsu$ git stash

Saved working directory and index state WIP on cat: e12d8ef add database.yml in config folder

HEAD is now at e12d8ef add database.yml in config folder
```

2.`git stash list`查詢中斷的項目  

```bash
tingdeAir:git-stash tingtinghsu$ git stash list

stash@{0}: WIP on cat: e12d8ef add database.yml in config folder
```

3.切換到master分支修改完bug，再切回自己手邊待做的branch

```bash
tingdeAir:git-stash tingtinghsu$ git checkout master
Switched to branch 'master'

...(做別的步驟)

tingdeAir:git-stash tingtinghsu$ git checkout cat
Switched to branch 'cat'
```

4.`git stash pop`

```bash
tingdeAir:git-stash tingtinghsu$ git stash pop
On branch cat
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   index.html

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (3e7ae10f684ba63b95b38ff8697cbd06f8c7179b)
```

重新回到之前的進度。

重點: `git stash apply` 或`git stash pop`兩者都可以使用，但`pop`指令會把原本的stash砍掉，`apply`保留stash

```bash
Switched to branch 'master'
tingdeAir:git-stash tingtinghsu$ git checkout cat
Switched to branch 'cat'
tingdeAir:git-stash tingtinghsu$ git stash apply
On branch cat
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   index.html

no changes added to commit (use "git add" and/or "git commit -a")
tingdeAir:git-stash tingtinghsu$ git stash list
stash@{0}: WIP on cat: e12d8ef add database.yml in config folder
```

## Case 4. 已經`git push`出去的進度該如何`reset`?

eg. 多人協作的情況下：想取消上次版本的其中一個commit

### 解法: 使用`reverse commit`

1.在source tree想要取消的那顆commit上按右鍵`reverse commit`  
2.`reverse commit`會做出新的commit，逆轉上次做的步驟（如果上次commit是新增檔案，這次commit就是刪除檔案）  

註:  
如果是只有自己一個人開發的專案，省略了協同合作上需要彼此溝通的情形，就可以直接`git reset`，commit再`git push -f`~(請參考[git reset筆記複習](https://tingtinghsu.github.io/blog/articles/2019-05-04-git_reset_tag))

## Case 5. 線上專案已被其他人執行`git push -f`

只能找最接近歷史的一次記錄再`git push -f`蓋回去，然後一個一個修改中間被改變過的檔案。（有夠麻煩）

要找到這個做壞事的人，然後對他說以後別這樣了！XD

# 相關連結

[HackMD筆記: Git小劇場 Part1](https://hackmd.io/G-H1vDSsT0CmGnoaBirteQ?view)  
[HackMD筆記: Git小劇場 Part2](https://hackmd.io/4_vOOL-sQEup4L9j1txZPg?view)  
[HackMD筆記: Git小劇場 Part3](https://hackmd.io/oXQUJX4gReu4vdFNrHUdRg?view)  
