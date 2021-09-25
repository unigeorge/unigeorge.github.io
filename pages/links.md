---
layout: page
title: Links
description: 
keywords: 友情链接
comments: false
menu: 链接
permalink: /links/
---

> 君子之交淡若水

<ul>
{% for link in site.data.links %}
  {% if link.src == 'life' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>

> 如有互加友链意向，请 [点此留言](/feedback/)

<ul>
{% for link in site.data.links %}
  {% if link.src == 'www' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>
