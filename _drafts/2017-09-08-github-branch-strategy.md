---
layout: post
title: github分支管理策略
categories: github
description: github分支管理策略
keywords: github,branch
---

## 介绍

我们采用Git-flow的分支结构和思想，中心仓库包含了两个主要分支，这两个分支的寿命是无限的：

* master
* develop

同时，我们会用到几个支持性分支：
* release
* hotfix

## 应用

### 开发要求

* 开发的同学不要向master分支提交代码，由负责发布的同学统一合并。

* 原则上，所有需要进入develop或release的代码都需要通过pull request进行代码review，review通过后再merge。

* 开发的同学，请先在github上fork仓库，切换到develop进行开发，开发完成后向中心仓库提交pull request。

* 进入stablelization阶段的Sprint，由负责发布的同学创建release分支，之后仅对release进行bug-fixing。待release达到发布状态时，将release合并到master分支，并tag；同时合并到develop分支。然后finish该release分支。

### 关于版本号

* 版本号组成：\<major\>.\<minor\>.\<hotfix\>
    + \<major\>: 主版本号，该版本号的递增会比较慎重，一般意味着可能有产品设计上的重构，可能存在兼容性变化。现阶段我们使用0，等产品上线时再讨论要不要升级到1.
    + \<minor\>: 从版本号，该版本号反应功能升级，随着每个sprint进行递增。
    + \<hotfix\>: 热修复版本号，在一个版本已经发布到线上，需要进行该版本的热修复时，递增该版本号。每个Sprint的第一个版本的hotfix号为0.

### 开发准备

1. fork
   >首先，从中心仓库Fork一个克隆仓库到自己github下（之后建议在setting里，将默认branch设置为develop）。
 
2. clone
   >将fork到自己账户下的仓库clone到本地（请使用https地址）；

3. add remote
   >本地终端运行如下命令，添加中心仓库的remote
    ```
    $ git remote add center [中心仓库地址]
    ```
至此，所有的准备工作已经完成，你可以在fork出的仓库下，切换到develop分支进行开发了。

### 代码提交及更新

```
// add要提交的文件
$ git add xxx.js   
 
// 添加描述并commit文件
$ git commit -m "feature descript"  
  
//更新center仓库代码到本地develop，此时如果有冲突，请本地手动解冲突，重新提交冲突文件
$ git pull center develop          
 
// push到你的fork仓库
$ git push      

```

现在，你可以到github上，在你fork的仓库下，发起Pull请求了（请注意检查merge的仓库是否正确）。

