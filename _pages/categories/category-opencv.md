---
layout: archive
title: "OpenCV"
permalink: /categories/opencv
author_profile: true
toc: true
---
{% for category in site.categories %}
  {% if category[0] == "OpenCV" %}
    {% for post in category[1] %}
      {% include archive-single.html type=list %}
    {% endfor %}
  {% endif %}  
{% endfor %}
