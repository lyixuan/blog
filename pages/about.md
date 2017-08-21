---
layout: page
title: About
description: 打码改变世界
keywords: YiXuan Liu
comments: true
menu: 关于
permalink: /about/
---

仰慕「优雅编码的艺术」

相信「习惯成就高效能」

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
