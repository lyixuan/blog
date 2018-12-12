---
layout: post
title: ESLint规则
categories: ESLint
description: ESLint
keywords: ESLint规则
---

ESLint 是在 ECMAScript/JavaScript 代码中识别和报告模式匹配的工具，它的目标是保证代码的一致性和避免错误。

* ESLint 使用 Espree 解析 JavaScript。
* ESLint 使用 AST 去分析代码中的模式
* ESLint 是完全插件化的。每一个规则都是一个插件并且你可以在运行时添加更多的规则。

** 1 禁用ESLint：
```
/* eslint-disable */
var a = 100;
console.log(a);
/* eslint-enable */
```

**2 禁用一条规则：
```
/*eslint-disable no-console */
var a = 100;
console.log(a);
/*eslint-enable no-console */
```

**3 调整规则：
```
/* eslint no-console:0 */
var a = 100;
console.log(a);
```
**4 在.eslintrc.js 中配置规则
* 见[ESLint配置]()

**5 错误级别

| 参数       | 描述            |
| ----------| --------------  |
| "off" or 0 | 关闭(禁用)规则   |
| "warn" or 1 | 将规则视为一个警告（并不会导致检查不通过） |
| "error" or 2 | 将规则视为一个错误 (退出码为1，检查不通过)  |
有些规则还带有可选的参数。

**6 常用规则

| 类别 | 描述 |
| --- | --- |
| salesAdviserIsPrincipal | 销售顾问为自己 |
| coachIsPrincipal | 教练为自己 |

