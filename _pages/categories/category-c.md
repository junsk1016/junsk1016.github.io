---
layout: archive
title: "C"
permalink: /categories/c
author_profile: true
toc: true
---
{% for category in site.categories %}
  {% if category[0] == "C" %}
    {% for post in category[1] %}
      {% include archive-single.html type=list %}
    {% endfor %}
  {% endif %}  
{% endfor %}
