---
layout: page
title: 16_PowerBi
---
{% for post in site.posts %}
  {% if post.category == 'PowerBi' %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
  {% endif %}
{% endfor %}
