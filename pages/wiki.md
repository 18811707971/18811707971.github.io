---
layout: page
title: technology
description: 精益求精
keywords: 技术, 学习
comments: false
menu: 快捷
permalink: /technology/
---

> 不一样的风景，不一样的终点
<ul class="listing">
{% for wiki in site.wiki %}
{% if wiki.title != "Wiki Template" %}
<li class="listing-item"><a href="{{ wiki.url }}">{{ wiki.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
