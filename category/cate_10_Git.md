---
layout: page
title: 10_Git
---
{% for post in site.posts %}
  {% if post.category == 'Git' %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
  {% endif %}
{% endfor %}
