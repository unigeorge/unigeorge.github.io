---
layout: page
title: Notes
description: 好记性不如烂笔头
keywords: 笔记, Note
comments: false
menu: 笔记
permalink: /notes/
---

> 好记性不如烂笔头！这里记录了一些技能基础和学习心得。

<ul class="listing">
{% for note in site.notes %}
{% if note.title != "Note Template" and note.topmost == true %}
<li class="listing-item"><a href="{{ site.url }}{{ note.url }}"><span class="top-most-flag">[置顶]</span>{{ note.title }}</a></li>
{% endif %}
{% endfor %}
{% for note in site.notes %}
{% if note.title != "Note Template" and note.topmost != true %}
<li class="listing-item"><a href="{{ site.url }}{{ note.url }}">{{ note.title }}<span style="font-size:12px;color:red;font-style:italic;">{%if note.layout == 'mindmap' %}  mindmap{% endif %}</span></a></li>
{% endif %}
{% endfor %}
</ul>
