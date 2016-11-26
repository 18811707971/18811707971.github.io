---
layout: page
title: technology
description: 精益求精
keywords: 技术, 学习
comments: false
menu: 技术宅
permalink: /technology/
---

> 记多少命令和快捷键会让脑袋爆炸呢？

<ul class="listing">
{% for wiki in site.wiki %}
{% if wiki.title != "Wiki Template" %}
<li class="listing-item"><a href="{{ wiki.url }}">{{ wiki.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
