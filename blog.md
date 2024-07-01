---
title: 日記
---

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ page.date }} {{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
