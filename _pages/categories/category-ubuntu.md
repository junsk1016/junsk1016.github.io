---
layout: archive
title: "Ubuntu"
permalink: /categories/ubuntu
author_profile: true
toc: true
---
{% for category in site.categories %}
  {% if category[0] == "Ubuntu" %}
    {% for post in category[1] %}
      {% include archive-single.html type=list %}
    {% endfor %}
  {% endif %}  
{% endfor %}
