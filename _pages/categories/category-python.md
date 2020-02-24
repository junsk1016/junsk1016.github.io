---
layout: archive
title: "Posts by Python"
permalink: /categories/python
author_profile: true
toc: true
---
{% for category in site.categories %}
  {% if category[0] == "Python" %}
    {% for post in category[1] %}
      {% include archive-single.html type=list %}
    {% endfor %}
  {% endif %}  
{% endfor %}
