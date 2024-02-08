---
title:  "Ruby on Rails練習 - MacOS安裝篇"
preview: "Install Ruby on Rails in MacOS environment"
permalink: "/articles/2018-08-21-install_ruby_rails_on_MacOS"
date:   2018-08-21 12:23:00
layout: post
tags: 
  - "rails"
comments: true
---
千里之行，始於足下。喊了要學Ruby on Rails好久，今天終於要來邁向第一步：安裝了！

一開始學習新的事物，主要就是跟著這個[網頁](http://installrails.com/steps/choose_os)所說的步驟 step by step。
很喜歡這個網頁的設計流程，透過大量圖示，讓新手除了能知道下一步該如何做，也包括Troubleshooting的部分。

不多說，趕快進入正題：

重點摘要:
* abstact
{:toc}

## A. 確定作業系統版本

我的電腦是MacOS High Siera Version10.13.6 (2018年8月)。  
  
![https://ithelp.ithome.com.tw/upload/images/20180821/20111177GwK0TkgPHe.png](https://ithelp.ithome.com.tw/upload/images/20180821/20111177GwK0TkgPHe.png)

## B. 安裝Xcode

我的Xcode是 Version 9.2 (2018年8月)。
這一步也許跟Command Line的設定有點關係。  
  
![https://ithelp.ithome.com.tw/upload/images/20181227/20111177DDz9Er1dV4.png](https://ithelp.ithome.com.tw/upload/images/20181227/20111177DDz9Er1dV4.png)

## C. 準備Terminal

我自己喜愛用另一個軟體叫[iTerm2](https://www.iterm2.com/)，跟蘋果內建的Terminal類似，某些情況時會兩個都打開一起用。  
  
![https://ithelp.ithome.com.tw/upload/images/20180821/20111177AhhjOzK0GK.png](https://ithelp.ithome.com.tw/upload/images/20180821/20111177AhhjOzK0GK.png)

## D. 安裝Homebrew

Homebrew是Mac的套件管理工具。只要裝好這個工具，未來就能使用指令裝應用程式了，不需要跑去下載安裝檔跟點開安裝！工程師就是喜歡把多個步驟節省成一個呢～～（*潮*） 細節可參考[這裏](https://www.ptt.cc/bbs/MAC/M.1451965224.A.4C4.html)

### d.1 Homebrew安裝指令

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### d.2 確定Homebrew版本

我的版本為Homebrew 1.4.0

```bash
brew -v
```

## E. 安裝Git

Git是分散式版本的版本控制系統(Version Control System)，對於工程師最重要的來說是可以清楚記錄每個檔案是誰在什麼時候加進來、什麼時候被修改或刪除，如果資料出現任何災情或意外時甚至可以復原到過去。詳情可參考龍哥的[Gitbook](https://gitbook.tw/chapters/introduction/what-is-git.html) 。
未來我也會花一些時間學習再整理出筆記寫下Git的功能，不過目前先裝好Git要緊～
這時剛剛的Homebrew馬上就派上用場囉！指令如下：

```bash
brew install git
```

每次安裝好一種玩具，最重要的是要確認裝好的是哪個版本囉！
我的git version 2.6.3 (2018年8月)。  

```ruby
git --version
```

## F. 設定Git

工程師通常會有個Git主機的帳號（例如Github）存放各種專案的程式碼。我們需要在自己的Command Line連上Git主機。我參考的是[龍哥的文章: Git教學：如何 Push 上傳到 GitHub？](https://gitbook.tw/chapters/github/push-to-github.html)

## G. 安裝RVM和Ruby

到了第七步驟，終於是安裝Ruby的部分了！（*敲碗*）  
RVM （Ruby Version Manager）可以幫你切換（安裝、管理、使用）不同版本的Ruby。  
請在Terminal輸入：

```ruby
\curl -L https://get.rvm.io | bash -s stable
```

安裝好RVM之後，關閉Terminal再重新打開，輸入以下指令確認版本。

```ruby
rvm | head -n 1
```

我的版本是Ruby enVironment Manager 1.29.3  
  
![https://ithelp.ithome.com.tw/upload/images/20180821/2011117716jHvmZ7ax.png](https://ithelp.ithome.com.tw/upload/images/20180821/2011117716jHvmZ7ax.png)

## H. 安裝Rails

第八步驟，精彩的來了:安裝Rails！  

Rails是使用Ruby語言所寫，按照MVC結構開發的開源Web應用框架，它是盡可能地保持簡單，使用最少的配置，並使實際的應用開發時的程式碼更少。以我家貓貓的話來說就是`工程師的好朋友`！

```bash
gem install rails --no-ri --no-rdoc
```

安裝完後老話一句，要確認玩具版本夠不夠新，請輸入版本查詢指令。
我的是Rails 5.1.6 (2018年8月)。  

```bash
rails --version
```

## I. 安裝IDE 編輯器

安裝一套跨平台的文字編輯器。市面上有太多種類的文字編輯器，例如`NotePad++`、`TextWrangler`等等，我目前使用的是[Sublime](http://www.sublimetext.com/)，點選連結後可下載安裝檔。

（這時候貓貓工程師在我耳朵旁邊OS打廣告:[VS code](https://code.visualstudio.com/) 好用！）

## J. 複習command line指令

現在可以開始寫程式囉！我們先在Terminal裡將常用的這三個指令好好熟記，如果你知道英文代表的意思的話，其實挺好記的。

```bash
1. cd	  切換目錄（change director）
2. pwd	取得目前所在的位置（print working director）
3. ls	  列出目前的檔案列表 (list)
```

在此作為範例，我們在Termminal移動到一個你適合放程式專案的路徑裡：

```bash
cd /Users/(你的電腦使用者名稱)/
```

建立一個新的Rails程式，輸入rails new 加上專案名稱：

```bash
$ rails new bater_loves_ting
```

移動到專案資料夾裡：

```bash
cd bater_loves_ting/
```

使用Terminal打開Sublime Text2:

```bash
subl .
```

![https://ithelp.ithome.com.tw/upload/images/20180821/20111177WalS8yl4IN.png](https://ithelp.ithome.com.tw/upload/images/20180821/20111177WalS8yl4IN.png)

出現了第一個Rails Ap！好的開始是成功的一半！  
慶祝一下～接下來還有路要耕耘呢：）

## L. 打開Rails Server

最後一步，開啟Server。
首先確定你移至了工作的app的路徑，

```bash
cd bater_loves_ting/
```

接著開啟伺服器：

```bash
rails server
```

![https://ithelp.ithome.com.tw/upload/images/20180821/20111177QAoxDreG9J.png](https://ithelp.ithome.com.tw/upload/images/20180821/20111177QAoxDreG9J.png)

檢查是否伺服器能順利啟動。移至瀏覽器，在網址欄位輸入本機地址：

>http://localhost:3000/

順利看到此圖的話，你就成功囉！
![https://ithelp.ithome.com.tw/upload/images/20180821/20111177fwSCQE9k8H.png](https://ithelp.ithome.com.tw/upload/images/20180821/20111177fwSCQE9k8H.png)

Ref:  

* [[Ting's筆記Day1] Ruby on Rails練習 - MacOS安裝篇](https://ithelp.ithome.com.tw/articles/10198937/)
* [Git教學：如何 Push 上傳到 GitHub？](https://gitbook.tw/chapters/github/push-to-github.html)