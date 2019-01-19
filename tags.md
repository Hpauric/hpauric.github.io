---
layout: default
title: Tags
permalink: /tags/
published: true
---

<ul>
{% for post in site.posts %}
  <li><a href="{{ post.url }}">{{ post.title }}</a> ({{ post.date | date_to_string }} | Tags: 
  {% for tag in post.tags %}
  {{tag}} 
  {% endfor %}
  )</li>
{% endfor %}
</ul>

