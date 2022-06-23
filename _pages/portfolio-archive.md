---
title: Hyper-Converged Infrastructure
layout: collection
permalink: /my_collection/
collection: my_collection
entries_layout: grid
classes: wide
---

{% for item in site.my_collection %}
  <h2>{{ item.title }}</h2>
  <p>{{ item.description }}</p>
  <p><a href="{{ item.url }}">{{ item.title }}</a></p>
{% endfor %}
