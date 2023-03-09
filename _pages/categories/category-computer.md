---
layout: archive
title: "Computer"
permalink: /categories/computer
author_profile: true
toc: true
---
{% for category in site.categories %}
  {% if category[0] == "Computer" %}
    {% for post in category[1] %}
      {% include archive-single.html type=list %}
    {% endfor %}
  {% endif %}  
{% endfor %}
