---
layout: page
title: Links
description: 
keywords: 友情链接
comments: false
menu: 链接
permalink: /links/
---

> 如有互加友链意向，可[点此留言](/feedback/)

> 本站的友链信息如下:

```
名称：{{ site.title }}
描述：{{ site.description }}
地址：{{ site.baseurl }}{{ site.url }}
头像：{{ site.baseurl }}{{ site.url }}/assets/images/qrcode.jpg
```

<ul>
{% for link in site.data.links %}
  {% if link.src == 'life' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>

<ul>
{% for link in site.data.links %}
  {% if link.src == 'www' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>
