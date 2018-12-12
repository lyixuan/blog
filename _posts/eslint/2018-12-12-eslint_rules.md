---
layout: post
title: 一个前后端分离系统的权限设计
categories: ESLint
description: ESLint
keywords: ESLint规则
---

# ESLint常见命令（规则表）

*1 禁用ESLint：*
```
/* eslint-disable */
var a = 100;
console.log(a);
/* eslint-enable */

```

Marble权限系统是控制marble用户访问的系统，它支持针对于角色的资源授权，提供一套API允许用户对权限进行增删查改等操作。

# 概念简介

## 基本概念

### Resource

在权限系统中，Resource可以表示可以被授权访问的主体。比如，在marble系统中，API接口可以算作一类资源，它可以通过配置的方式允许特定用户对其调用；模块也是一种资源，比如课程表模块，它可以允许用户对其进行查看。

资源使用Ari来表示，它的结构如下：

```
ari:{service}:{region}:{account}:{resourceType}:{resource}
```

* {service} - 服务名称，对于marble系统，固定为 `marble`
* {region} - 地域名称，目前为空
* {account} - 账号ID，对于marble系统，此为资源所在的projectId，如果资源不属于某个project为空
* {resourceType} - 资源类型，如branchModule，method等。
* {resource} - 资源名，也就是资源的相对路径，比如 `projects/1/branches/1/modules/member/potential_student`


### Action

表示对资源的操作，比如对于场地模块，它提供了create（新建）、view（查看）、update（修改）操作。

### Effect

表示为禁止访问（allow）还是允许访问（deny）

### Condition

表示访问时满足的条件，比如对修改潜在会员api进行调用时，如果其修改的潜在会员的salesAdviserId为1，则此次访问满足 `ParamMatch: {"salesAdviserId": [1]}` 这个条件。

## Privilege

Privilege是指对某个资源的某个操作在某种条件下的限制策略（允许还是禁止），它是Resource，Action，Effect，Condition的组合；一个实例的Privilege如下：

```
    {
      "resource": "ari:marble::1:branch_module:projects/1/branches/1/modules/member/potential_student",
      "action": "update",
      "effect": "allow",
      "condition": {
        "actMatch": [
          "salesAdviserIsPrincipal"
        ]
      }
    }
```

如果某个用户具有此Privilege，则当其对于某个branch（projectId为1，branchId为1的branch）下的potential_student模块进行update操作时（修改潜在会员信息），且修改的潜在会员信息的销售顾问是自己时（符合condition），可以允许其访问。

### 权限的检查方式

每个用户都有一组权限列表，当用户对某个资源进行某个操作的时候，系统会取出此用户具有的此资源此操作的所有Privilege，并逐条匹配Condition是否符合当前操作，并通过以下规则判断是否允许用户继续操作：

* 当某条Privilege的condition匹配当前操作，且effect为deny，拒绝此操作。
* 当某条Privilege的condition匹配当前操作，且effect为allow，且没有符合条件的deny操作，允许此操作。
* 当没有任何Privilege的condition匹配，拒绝此操作

## 可授权的资源

### API资源

是指访问某个API的权限，此资源只有一种操作即 `execute` ，表示请求此api。目前没有接口直接允许用户设置API的授权，其授权通过模块授权间接完成

### Module资源（模块资源）

模块是一个前端的概念，比如会员模块表示前端对会员的相关操作；同时模块映射了一组API，对模块的授权可以间接允许或拒绝用户对某个API的访问。

模块分成两类，project模块以及branch模块。

project模块的ari表示如下：

```
ari:marble::{projectId}:project_module:projects/{projectId}/modules/{moduleName}
``` 

branch模块的ari表示如下：

```
ari:marble::{projectId}:branch_module:projects/{projectId}/branches/{branchId}/modules/{moduleName}
``` 

模块的condition现在仅支持 `actMatch` , 有如下选项：

| 名称 | 说明 |
| --- | --- |
| salesAdviserIsPrincipal | 销售顾问为自己 |
| coachIsPrincipal | 教练为自己 |

