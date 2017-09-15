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
    $ git config --global user.name "John Doe"
    $ git config --global user.email johndoe@example.com 
    
    ```
    
## 2、添加一个远程地址别名
* 1

   ```
    $ git remote add origin [中心仓库地址]
   ```

## 3、分支合并与解冲突

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
     
    git pull origin master ----allow-unrelated-histories
     
    git push origin master
    
    ```
