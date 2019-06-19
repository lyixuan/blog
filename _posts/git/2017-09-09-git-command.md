---
layout: post
title: git操作详解
categories: git
description: git操作详解
keywords: git
---

## 1、你的标识

* 当你安装Git后首先要做的事情是设置你的用户名称和e-mail地址。这是非常重要的，因为每次Git提交都会使用该信息。它被永远的嵌入到了你的提交中。
    
  ```
    git config --global user.name "John Doe"
    git config --global user.email johndoe@example.com 
    ```
    

## 2、分支合并与解冲突

* 将develop分支merge到feature分支
   
  ``` 
    git checkout develop
    
    git pull
    
    git checkout feature
    
    git merge develop   // 将develop merge到当前分支
    
    git status    // 查看merge状态，如果有冲突文件，打开IDE手动解除冲突
    
    // 提交冲突文件
    
    git add xx.js     
    
    git commit
    
    git push origin feature
        
  ``` 
* 如果是两个不同remote源的合并

   >从远程库获取某个分支的更新，再与本地指定分支进行自动merge，格式如下

  ```
    git pull <远程库名><远程分支名>:<本地分支名>
  ``` 
  
  >比如，取回远程remote命名为center的master分支，与本地develop进行merge

  ```
    git pull center master:develop
  ``` 
 
  >如果要与本地当前分支进行merge，冒号后的<本地分支名>可以省略

  ```
    git pull center master:develop
  ``` 

## 3、初始化版本库，并提交到远程服务器端

* 下面的命令会增加URL地址为'git@github.com:daixu/WebApp.git'，名称为origin的远程服务器库，以后提交代码的时候只需要使用 origin别名即可

  ```
    mkdir WebApp
    
    cd WebApp
    
    git init 本地初始化
    
    touch README
    
    git add README 添加文件
    
    git commit -m 'first commit'
    
    git remote add origin git@github.com:daixu/WebApp.git  增加一个远程服务器端
    
    git pull origin master --allow-unrelated-histories  git新版本需要
    
    git push 

   ``` 
   

## 4、远程仓库变更地址

* 1.不要之前的提交记录

  删除本地.git文件夹
  
  ```
  
   git init  
    
   git add -A    
    
   git remote add origin https://git.coding.net/**/**.git
     
   git push -u origin master
   
   ```
   
* 2.若要保存之前的提交记录

  ```
    git remote rm origin
       
    git remote add origin https://git.coding.net/**/**.git
       
    git pull origin master --allow-unrelated-histories    
     
    git push origin master
     
    或者
     
    git remote origin set-url https://git.coding.net/**/**.git
     
    git pull origin master --allow-unrelated-histories
     
    git push origin master
    
    ```
## 5、删除远程和本地分支

  
  ```
  liuyixuan:sd_inspector_admin lyx$ git branch -a      // 查看所有分支.其中，remote/origin/master表示的是远程分支
      development
      master
      quality
    * score_appeal
      remotes/origin/HEAD -> origin/master
      remotes/origin/development
      remotes/origin/master
      remotes/origin/quality

    
   git push origin --delete quality              // 可以删除远程分支  quality
    
   git branch -d quality         // 可以删除本地分支  quality
   ```
   注：不能删除当前分支，如删除需先切换到其他分支
   
## 6、撤销合并

  * 1.执行 git merge dev后，需要解冲突。这是想要撤销这次操作，可执行以下命令
  ```
  git merge --abort
   ```
   
## git常用命令速查

* git branch 查看本地所有分支
* git status 查看当前状态 
* git commit 提交 
* git branch -a 查看所有的分支
* git branch -r 查看远程所有分支
* git commit -am "init" 提交并且加注释 
* git remote add origin [仓库地址]  添加一个远程地址别名 
* git push origin master 将文件给推到服务器上 
* git remote show origin 显示远程库origin里的资源 
* git push origin master:develop
* git push origin master:hb-dev 将本地库与服务器上的库进行关联 
* git checkout --track origin/dev 切换到远程dev分支
* git branch -D master develop 删除本地库develop
* git checkout -b dev 建立一个新的本地分支dev
* git merge origin/dev 将分支dev与当前分支进行合并
* git checkout dev 切换到本地dev分支
* git remote show 查看远程库
* git add .
* git rm 文件名(包括路径) 从git中删除指定文件
* git clone git://github.com/schacon/grit.git 从服务器上将代码给拉下来
* git config --list 看所有用户
* git ls-files 看已经被提交的
* git rm [file name] 删除一个文件
* git commit -a 提交当前repos的所有的改变
* git add [file name] 添加一个文件到git index
* git commit -v 当你用－v参数的时候可以看commit的差异
* git commit -m "This is the message describing the commit" 添加commit信息
* git commit -a -a是代表add，把所有的change加到git index里然后再commit
* git commit -a -v 一般提交命令
* git log 看你commit的日志
* git diff 查看尚未暂存的更新
* git rm a.a 移除文件(从暂存区和工作区中删除)
* git rm --cached a.a 移除文件(只从暂存区中删除)
* git commit -m "remove" 移除文件(从Git中删除)
* git rm -f a.a 强行移除修改后文件(从暂存区和工作区中删除)
* git diff --cached 或 $ git diff --staged 查看尚未提交的更新
* git stash push 将文件给push到一个临时空间中
* git stash pop 将文件从临时空间pop下来
* git remote add origin git@github.com:username/Hello-World.git
* git push origin master 将本地项目给提交到服务器中
* git pull 本地与服务器端同步
* git push (远程仓库名) (分支名) 将本地分支推送到服务器上去。
* git push origin serverfix:awesomebranch
* git fetch 相当于是从远程获取最新版本到本地，不会自动merge
* git commit -a -m "log_message" (-a是提交所有改动，-m是加入log信息) 本地修改同步至服务器端 ：
* git branch branch_0.1 master 从主分支master创建branch_0.1分支
* git branch -m branch_0.1 branch_1.0 将branch_0.1重命名为branch_1.0
* git checkout branch_1.0/master 切换到branch_1.0/master分支
* du -hs
* git branch 删除远程branch
* git push origin :branch_remote_name
* git branch -r -d branch_remote_name

