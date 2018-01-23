---
layout: post
title: 一个前后端分离系统的权限设计
categories: design
description: design
keywords: privilege
---

# 概述

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

## Role （角色）

角色可以拥有一组privilege，用户可以属于一个或者多个角色；当用户属于一个角色时，其会拥有这个角色所有的权限。


# 权限系统API

## 获取所有module信息

* *语法*

获取project级别下的模块

```
GET /api/v1/projects/{projectId}/modules
```

获取branch级别下的模块

```
GET /api/v1/projects/{projectId}/branches/{branchId}/modules
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |

* *返回参数*

| 参数 | 说明 |
| --- | --- |
| modules | 模块列表 |
| modules.id | 模块id |
| modules.name | 模块名称 |
| modules.displayName | 模块显示名称 |
| modules.actions | 模块支持的操作列表 |
| modules.actions.name | 操作名称 |
| modules.actions.displayName | 操作显示名称 |
| modules.actions.options | 操作参数列表 |
| modules.actions.options.name | 操作参数名称 |
| modules.actions.options.type | 操作参数类型，现在只支持 actMatch |
| modules.actions.options.displayName | 操作参数显示名称 |

* *示例*

请求：

```
GET /api/v1/projects/1/branches/1/modules HTTP/1.1
[request headers ...]

```

返回：

```
HTTP/1.1 200 OK
[response headers ...]

{
    modules: [
        {
            "name": "member/guardianStudent",
            "displayName": "会员管理",
            "actions": [
                {
                    "name": "list",
                    "displayName": "获取列表",
                    "options": [
                        {"name": "salesAdviserIsPrincipal", "type": "actMatch", "displayName": "销售顾问为自己"}
                    ]
                }
            ]
        }
    ]
}
```

## 新建role

* *语法*

```
POST /api/v1/projects/{projectId}/branches/{branchId}/roles
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |

* *请求元素*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| name | 是 | 角色名称 |
| comment | 否 | 备注信息 |

* *返回元素*

| 名称 | 说明 |
| --- | --- |
| id | 角色id |

* *示例*

请求：

```
POST /api/v1/projects/1/branches/1/roles HTTP/1.1
[request headers ...]

{
    "name": "角色1",
    "comment": "测试角色"
}

```

返回

```
HTTP/1.1 201 Created
[response headers ...]

{
    "id": 1
}
```

## 获取role列表

* *语法*

```
GET /api/v1/projects/{projectId}/branches/{branchId}/roles
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |

* *请求元素*

无

* *返回元素*

| 名称 | 说明 |
| --- | --- |
| id | 角色id |
| name | 角色名称 |
| privileges | 角色所有的权限 |

* *示例*

请求：

```
GET /api/v1/projects/1/branches/1/roles HTTP/1.1
[request headers ...]

```

返回：

```
HTTP/1.1 200 OK
[response headers ...]

{
    "roles": [
        {
            "id": 1,
            "name": "角色1",
            "privileges": [
                {
                    "resource": "ari:marble::1:branch_module:projects/1/branches/1/modules/member/student"
                    "action": "list",
                    "effect": "allow",
                    "condition": {
                        "actMatch": ["salesAdviserIsPrincipal"]
                    }
                }
            ]
        },
        {
            "id": 2,
            "name": "角色2",
            "privileges": [

            ]
        }
    ]
}
```

## 获取role的信息

* *语法*

```
GET /api/v1/projects/{projectId}/branches/{branchId}/roles/{roleId}
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| roleId | 是 | 角色id |

* *请求元素*

无

* *返回元素*

| 名称 | 说明 |
| --- | --- |
| id | 角色id |
| name | 角色名称 |
| privileges | 角色所有的权限 |

* *示例*

请求：

```
GET /api/v1/projects/1/branches/1/roles HTTP/1.1
[request headers ...]

```

返回：

```
HTTP/1.1 200 OK
[response headers ...]

{
    "id": 1,
    "name": "角色1",
    "privileges": [
        {
            "resource": "ari:marble::1:branch_module:projects/1/branches/1/modules/member/student"
            "action": "list",
            "effect": "allow",
            "condition": {
                "actMatch": ["salesAdviserIsPrincipal"]
            }
        }
    ]
}
```

## 修改role

* *语法*

```
POST /api/v1/projects/{projectId}/branches/{branchId}/roles/{roleId}
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| roleId | 是 | 角色id |

* *请求元素*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| name | 是 | 角色名称 |
| comment | 否 | 备注信息 |

* *返回元素*

无

* *示例*

请求：

```
PUT /api/v1/projects/1/branches/1/roles/1 HTTP/1.1
[request headers ...]

{
    "name": "角色1",
    "comment": "测试角色"
}

```

返回

```
HTTP/1.1 200 OK
[response headers ...]

```

## 删除role

* *语法*

```
DELETE /api/v1/projects/{projectId}/branches/{branchId}/roles/{roleId}
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| roleId | 是 | 角色id |

* *请求元素*

无

* *返回元素*

无

* *示例*

请求：

```
DELETE /api/v1/projects/1/branches/1/roles/1 HTTP/1.1
[request headers ...]

```

返回

```
HTTP/1.1 200 OK
[response headers ...]

```

## 向role新增权限

* *语法*

```
POST /api/v1/projects/{projectId}/branches/{branchId}/roles/{roleId}/privileges
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| roleId | 是 | role的id |

* *请求元素*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| privileges | 是 | 权限列表 |
| privileges.module | 是 | 模块名称 |
| privileges.action | 是 | 操作名称 |
| privileges.condition | 是 | 操作条件 |

* *返回元素*

| 名称 | 说明 |
| --- | --- |
| id | 权限id |

* *示例*

请求：

```
POST /api/v1/projects/1/branches/1/roles/1 HTTP/1.1
[request headers ...]

{
    "privileges": [
        {
            "resource": "ari:marble::1:branch_module:projects/1/branches/1/modules/member/guardianStudent"
            "action": "list",
            "effect": "allow",
            "condition": {
                "actMatch": ["salesAdviserIsPrincipal"]
            }
        }
    ]
}
```

返回

```
HTTP/1.1 200 OK
[response headers ...]

{
    "privileges": [
        {
            "id": 1
        },
        {
            "id": 2
        }
    ]
}
```

## 删除role的权限

* *语法*

```
DELETE /api/v1/projects/{projectId}/branches/{branchId}/roles/{roleId}/privileges/{privilegeId}
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| roleId | 是 | role的id |
| roleId | 是 | 权限的id |

* *请求元素*

无

* *返回元素*

无

* *示例*

请求：

```
DELETE /api/v1/projects/1/branches/1/roles/1/privileges/1 HTTP/1.1
[request headers ...]

```

返回

```
HTTP/1.1 200 OK
[response headers ...]

```

## 设置员工的role

* *语法*

```
PUT /api/v1/projects/{projectId}/branches/{branchId}/employees/{employeeId}/roles
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| employeeId | 是 | 员工id |

* *请求元素*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| roles | 是 | 角色列表 |
| roles.id | 是 | 角色id |

* *返回元素*

无

* *示例*

请求：

```
PUT /api/v1/projects/1/branches/1/roles/1/employees HTTP/1.1
[request headers ...]

{
    "roles": [
        {
            "id": "1"
        }
    ]
}
```

返回

```
HTTP/1.1 200 OK
[response headers ...]

```

## 获取用户权限列表

* *语法*

```
GET /api/v1/projects/{projectId}/employees/{employeeId}/privileges
```


* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| employeeId | 是 | 员工id |

* *请求元素*

无

* *返回元素*

| 名称 | 说明 |
| --- | --- |
| privileges | 角色所有的权限 |

* *示例*

请求

```
GET /api/v1/projects/1/employees/1/privileges HTTP/1.1
[request headers ...]

{
    "privileges": [
        {
            "resource": "ari:marble::1:module:projects/1/branches/1/modules/education/time_table"
            "action": "view",
            "effect": "allow",
            "condition": {
                "actMatch": ["salesAdviserIsPrincipal"]
            }
        }
    ]
}
```

## 设置用户可登陆状态

* *语法*

```
PUT /api/v1/projects/{projectId}/branches/{branchId}/employees/{employeeId}/permission
```


* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| employeeId | 是 | 员工id |

* *请求元素*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| enableLogin | 是 | 是否可登陆 |

* *返回元素*

无

* *示例*

请求

```
PUT /api/v1/projects/1/branches/1employees/1/permission HTTP/1.1
[request headers ...]

{
    "enableLogin": true
}
```

## 重置员工密码

* *语法*

```
POST /api/v1/projects/{projectId}/branches/{branchId}/employees/{employeeId}/password
```


* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| employeeId | 是 | 员工id |

* *请求元素*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| operatorPassword | 是 | 操作人的密码 |

* *返回元素*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| password | 是 | 重置后的密码 |

* *示例*

请求

```
POST /api/v1/projects/1/branches/1employees/1/password HTTP/1.1
[request headers ...]

{
    "operatorPassword": "dfewrew"
}
```

## 查询员工列表（权限相关）

* *语法*

```
GET /api/v1/projects/{projectId}/branches/{branchId}/employee_permissions?
queryType={queryType}&
keyword={keyword}&
positionId={positionId}&
dutyId={dutyId}&
departmentId={departmentId}&
reverse={reverse}&
page={page}&
pagesize={pagesize}
```

* *请求参数*

| 参数 | 是否必须 | 说明 |
| --- | --- | --- |
| projectId | 是 | project的id |
| branchId | 是 | branch的id |
| queryType | 否 | 条件搜索"rule", 关键字搜索"keyword" |
| keyword | 否 | 搜索关键字 |
| positionId | 否 | 职位id |
| dutyId | 否 | 岗位id |
| departmentId | 否 | 部门id |
| reverse | 否 | 是否反向搜索 |
| page | 否 | 指定第n页，从第1页开始，默认1 |
| pagesize | 否 | 指定每页数据条数，默认20 |

* *返回元素*

| 名称 | 说明 |
| --- | --- |
| id | 员工id |
| name | 员工名称 |
| branchId | 分支id |
| branchName | 分支名称 |
| departmentId | 部门id |
| departmentName | 部门名称 |
| code | 员工工号 |
| positionId | 职位id |
| positionName | 职位名称 |
| duties | 岗位列表 |
| duties.dutyId | 岗位id |
| duties.dutyName | 岗位名称 |
| enableLogin | 是否允许登录 |
| roles | 员工的role |
| roles.id | 角色id |
| roles.name | 角色名称 |

* *示例*

请求

```
GET /api/v1/projects/1/branches/1/employee_permissions HTTP/1.1

```

返回

```
HTTP/1.1 200 OK
[response headers ...]

{
  "employeePermissions": [
    {
      "id": 5,
      "branchId": 1,
      "branchName": "大屯分部",
      "departmentId": 2,
      "departmentName": "销售部",
      "code": "ec00004",
      "positionId": 3,
      "positionName": "员工",
      "duties": [
        {
          "dutyId": 4,
          "dutyName": "人事"
        }
      ],
      "name": "员工4（人事）",
      "enableLogin": true,
      "roles": []
    },
    {
      "id": 1,
      "branchId": 1,
      "branchName": "大屯分部",
      "departmentId": 4,
      "departmentName": "教学部",
      "code": "ec00000",
      "positionId": 2,
      "positionName": "部门主管",
      "duties": [
        {
          "dutyId": 1,
          "dutyName": "销售"
        },
        {
          "dutyId": 2,
          "dutyName": "教练"
        },
        {
          "dutyId": 3,
          "dutyName": "行政"
        },
        {
          "dutyId": 4,
          "dutyName": "人事"
        }
      ],
      "name": "员工0（ALL）",
      "enableLogin": true,
      "roles": [
        {
          "id": 1
        }
      ]
    }
  ],
  "totalPages": 1
}
```

# 附：模块列表

## 教务模块

### 活动管理

* 模块名称: `education/activity`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建活动 |  无 |
| view | 查看活动 |  无 |
| update | 修改活动信息 |  无 |
| delete | 删除活动 |  无 |
| add_student | 添加学生到活动 |  无 |
| view_student | 查看参与活动的学生 |  无 |
| update_student | 修改学生活动信息 |  无 |
| remove_student | 从活动中移除学生 |  无 |
| cost | 扣费 |  无 |


### 班级管理

* 模块名称: `education/classe`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建班级 |  无 |
| list | 查看班级列表 |  无 |
| view | 查看班级详细信息 |  无 |
| update | 修改班级 |  无 |
| delete | 删除班级 |  无 |
| list_student | 查看班级学生列表 |  无 |
| update_student_list | 向班级添加、删除学生 |  无 |
| do_adjustment | 班级调课 |  无 |
| view_adjustment | 查看班级调课 |  无 |


### 课程管理

* 模块名称: `education/course`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建课程 |  无 |
| view | 查看课程 |  无 |
| update | 修改课程信息 |  无 |
| delete | 删除课程 |  无 |


### 刷卡/上课管理

* 模块名称: `education/course_cost`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看 |  无 |
| cost | 消费/修改上课状态 |  无 |


### 场地管理

* 模块名称: `education/field`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建场地 |  无 |
| view | 查看场地 |  无 |
| update | 修改场地信息 |  无 |


### 体测项目管理

* 模块名称: `education/fitnessItem`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建体测项目 |  无 |
| view | 查看体测项目 |  无 |
| update | 修改体测项目 |  无 |
| delete | 删除体测项目 |  无 |


### 消息推送

* 模块名称: `education/guardian_message`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 发送推送 |  无 |
| view | 查看推送 |  无 |


### 短信管理

* 模块名称: `education/guardian_sms_message`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 发送短信 |  无 |
| view | 查看短信 |  无 |


### 教学大纲管理

* 模块名称: `education/plan`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建大纲 |  无 |
| view | 查看大纲 |  无 |
| update | 修改大纲 |  无 |


### 课程表

* 模块名称: `education/time_table`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看课程表 |  无 |

## 会员管理

### 会员卡管理

* 模块名称: `member/card`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建会员卡 |  无 |
| view | 查看会员卡 |  无 |
| update | 修改会员卡信息 |  无 |


### 会员管理

* 模块名称: `member/guardianStudent`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建会员/家长 |  无 |
| list | 查询会员/家长列表 | salesAdviserIsPrincipal |
| view | 查看会员/家长信息 | salesAdviserIsPrincipal |
| update | 修改会员/家长信息 |  无 |
| view_costs | 查看消费记录 |  无 |
| update_password | 重置家长端密码 |  无 |
| create_balance | 购买课包 |  无 |
| view_balance | 查看课包 |  无 |
| update_balance | 修改课包 |  无 |
| assign_course | 为学生安排班级/调课 |  无 |


### 潜在会员管理

* 模块名称: `member/potential_student`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建潜在会员 |  无 |
| list | 查看潜在会员列表 | salesAdviserIsPrincipal |
| view | 查看潜在会员信息 | salesAdviserIsPrincipal |
| update | 修改潜在会员信息 |  salesAdviserIsPrincipal |
| delete | 删除潜在会员 |  无 |
| assign_sales | 为潜在会员分配销售 |  无 |
| assign_course | 为潜在会员安排试听 | salesAdviserIsPrincipal |


### 跟进记录管理

* 模块名称: `member/potential_student_progress`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建跟进记录 | salesAdviserIsPrincipal |
| view | 查看跟进记录 | salesAdviserIsPrincipal |
| delete | 删除跟进记录 | salesAdviserIsPrincipal |


### 考勤管理

* 模块名称: `staffing/attendance`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建考勤记录 |  无 |
| view | 查看考勤记录 |  无 |
| update | 修改考勤记录 |  无 |
| delete | 删除考勤记录 |  无 |


### 招聘管理

* 模块名称: `staffing/candidate`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建候选人 |  无 |
| view | 查看候选人信息 |  无 |
| update | 修改候选人信息和状态 |  无 |


### 员工管理

* 模块名称: `staffing/employee`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建员工 |  无 |
| view | 查看员工 |  无 |
| update | 更新员工信息 |  无 |


### 薪资管理

* 模块名称: `staffing/salary`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看员工薪资 |  无 |
| update | 修改员工薪资 |  无 |
| delete | 删除员工薪资信息 |  无 |


### 培训管理

* 模块名称: `staffing/training`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建培训 |  无 |
| list | 查看培训列表 |  无 |
| view | 查看培训详细信息 |  无 |
| update | 修改培训信息 |  无 |
| delete | 删除培训 |  无 |


## 数据统计


### 教练统计

* 模块名称: `statistics/coach_statistics`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看 |  无 |

### 体测数据统计

* 模块名称: `statistics/fitness`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看 |  无 |


### 地推统计

* 模块名称: `statistics/push_statistics`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看 |  无 |


### 销售统计

* 模块名称: `statistics/sales_statistics`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看 |  无 |

## 系统管理

### 员工权限设置

* 模块名称: `system_management/employee_permission`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看权限 |  无 |
| update | 更新权限 |  无 |
| update_passoword | 重置员工密码 |  无 |

### 角色管理

* 模块名称: `system_management/role`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建角色 |  无 |
| view | 查看角色 |  无 |
| update | 修改角色信息 |  无 |
| delete | 删除角色 |  无 |

### 渠道管理

* 模块名称: `system_management/static_resource/channel`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建渠道 |  无 |
| view | 查看渠道 |  无 |
| update | 修改渠道名称 |  无 |
| delete | 删除渠道 |  无 |


### 小区管理

* 模块名称: `system_management/static_resource/community`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建小区 |  无 |
| view | 查看小区 |  无 |
| update | 修改小区名称 |  无 |
| delete | 删除小区 |  无 |


### 部门管理

* 模块名称: `system_management/static_resource/department`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建部门 |  无 |
| view | 查看部门 |  无 |
| update | 修改部门信息 |  无 |
| delete | 删除部门 |  无 |


### 岗位管理

* 模块名称: `system_management/static_resource/job_duty`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建岗位 |  无 |
| view | 查看岗位信息 |  无 |
| update | 修改岗位信息 |  无 |
| delete | 删除岗位 |  无 |


### 家长职业管理

* 模块名称: `system_management/static_resource/occupation`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建家长职业 |  无 |
| view | 查看家长职业信息 |  无 |
| update | 修改家长职业信息 |  无 |
| delete | 删除家长职业 |  无 |


### 课包管理

* 模块名称: `system_management/static_resource/pack`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建课包 |  无 |
| view | 查看课包列表 |  无 |
| update | 修改课包 |  无 |
| delete | 删除课包 |  无 |


### 职位管理

* 模块名称: `system_management/static_resource/position`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建职位 |  无 |
| view | 查看职位信息 |  无 |
| update | 修改职位信息 |  无 |
| delete | 删除职位 |  无 |


### 地推区域管理

* 模块名称: `system_management/static_resource/push_area`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建地推区域 |  无 |
| view | 查看地推区域 |  无 |
| update | 修改地推区域信息 |  无 |
| delete | 删除地推区域 |  无 |


### 学校管理

* 模块名称: `system_management/static_resource/school`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| create | 新建学校 |  无 |
| view | 查看学校 |  无 |
| update | 修改学校 |  无 |
| delete | 删除学校 |  无 |

## 教学

### 课堂记录

* 模块名称: `teaching/course_log`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看课堂详细记录 | coachIsPrincipal |
| list | 查看上课列表 | coachIsPrincipal |
| update | 修改课堂记录 | coachIsPrincipal |


### 教学进度

* 模块名称: `teaching/student_learning_progress`
* 权限列表

| 权限 | 说明 | 支持选项 |
| --- | --- | --- |
| view | 查看 |  无 |
    


# web端权限配置

web端通过两个属性指令： check-permission 和 with-context 控制元素的显示。

想要根据权限策略控制一个元素的显示，只需要给这个元素增加check-permission(必须项) 和 with-context(附加项) 两个属性及相应的值即可。

* check-permission 的值包含了权限的资源类型、模块名、操作类型三项，之间用":"分割。

* check-permission 的特殊值:"ignore"。一个元素不受权限控制保持显示，可以不添加check-permission属性，或者将check-permission值置为"ignore"。

* with-context 的值包含了权限的附加条件。条件格式由外到内分别用 "," , ":" , "&" , "="分割。如果一个权限即一个privilege带有condition，那么相应的元素也必须有with-context和对应的值，否则 (没有with-context或值不符) 判定为无显示权限。

* with-context 的特殊值:"all"。如果一个元素的with-context属性赋予了"all"值,该权限的检查策略为如果这个权限策略没有完全deny，则视为有显示权限。

* 显示权限取反。在check-permission属性的值前面加"!",表示该元素的显示取反(权限判定策略不变)。

| 属性 | 类型 | 格式 |
| --- | --- | --- |
| check-permission | string |  资源类型：模块名：操作名  |
| with-context  | string |  类型1：条件名1=值1 &条件名2=值2，类型2：条件名1=值1 &条件名2=值2  或者 "all"|

* *示例*

```
<div check-permission="branch:member/potential_student:view" with-context="actMatch:salesAdviserIsPrincipal=1"></div>

<div check-permission="branch:member/potential_student:list" with-context="all"></div>

<div check-permission="branch:member/potential_student:delete"></div>
```

