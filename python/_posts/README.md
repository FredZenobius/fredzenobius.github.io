---
layout: category
title: Python
description: >
  Python 글 목록
---

<ul>
  {% for post in site.categories.python %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
