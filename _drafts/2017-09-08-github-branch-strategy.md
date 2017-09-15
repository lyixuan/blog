---
layout: post
title: github分支管理策略
categories: github
description: github分支管理策略
keywords: github,branch
---

## 介绍

建议采用Git-flow的分支结构和思想，中心仓库包含了两个主要分支，这两个分支的寿命是无限的：

* master
* develop

同时，我们会用到几个支持性分支：
* feature
* release
* hotfix

## 应用

### 开发要求

* 开发的同学不要向master分支提交代码，由负责发布的同学统一合并。
* 原则上，所有需要进入develop或release的代码都需要通过pull request进行代码review，review通过后再merge。但我们也不搞形式主义，对于个别不需要review的提交（如改一下版本号），也可以直接提交进develop或相应地release分支，但一定要确保改动足够小，并且不会出现错误。
* 在进行最新Sprint开发时，请使用feature分支，开发完成后向develop提交pull request。Pull合并完成后，finish相应的feature分支。
* 进入stablelization阶段的Sprint，由负责发布的同学创建release分支，之后仅对release进行bug-fixing。待release达到发布状态时，将release合并到master分支，并tag；同时合并到develop分支。然后finish该release分支。

### 关于版本号

* 版本号组成：\<major\>.\<minor\>.\<hotfix\>
    + \<major\>: 主版本号，该版本号的递增会比较慎重，一般意味着可能有产品设计上的重构，可能存在兼容性变化。现阶段我们使用0，等产品上线时再讨论要不要升级到1.
    + \<minor\>: 从版本号，该版本号反应功能升级，随着每个sprint进行递增。
    + \<hotfix\>: 热修复版本号，在一个版本已经发布到线上，需要进行该版本的热修复时，递增该版本号。每个Sprint的第一个版本的hotfix号为0.

### 开发建议

* 软件开发中，总有无穷无尽的新的功能要不断添加进来。添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，发起pull请求，等待合并，最后，删除该feature分支。

* Feature分支名建议简短但能描述功能，命名格式：feature-*。

* Hotfix分支命名格式：hotfix-*。

### 一个示例  

现在，你终于接到了一个新任务：开发代号为Vulcan的新功能，该功能计划用于下一代人工智能。
于是准备开发：

```
$ git branch    // 查看当前分支
 *develop
  master

$ git checkout develop   // 如果不在develop，切换到develop分支
Already on 'develop'

$ git pull    // pull最新代码，保持远程同步
Already up-to-date.

$ git checkout -b feature-vulcan    // 新建并切换到feature-vulcan分支
Switched to a new branch 'feature-vulcan'
```
5分钟后，开发完毕：

```
$ git add vulcan.py   // add文件

$ git commit -m "add feature vulcan"  // 添加描述并commit文件
[vulcan eafcdd8] branch test
 1 file changed, 1 deletion(-)

$ git push   // push到远程仓库
Everything up-to-date

```

现在，你可以到github上发起Pull请求了。

请求者在github上对该分支发起pull请求，并write some comment，等待merge。

审核者可以write some comment  或者 merge pull request。分支被merge后，就可以删除该分支了。
