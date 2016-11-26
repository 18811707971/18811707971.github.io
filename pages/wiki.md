---
layout: page
title: technology
description: 精益求精
keywords: 技术, 学习
comments: false
menu: 技术宅
permalink: /technology/
---

> 捷径，老司机带你飞

<ul class="listing">
{% for wiki in site.wiki %}
{% if wiki.title != "Wiki Template" %}
<li class="listing-item"><a href="{{ wiki.url }}">{{ wiki.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
