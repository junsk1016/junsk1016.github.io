---
layout: archive
title: "Atom"
permalink: /categories/atom
author_profile: true
toc: true
---
{% for category in site.categories %}
  {% if category[0] == "Atom" %}
    {% for post in category[1] %}
      {% include archive-single.html type=list %}
    {% endfor %}
  {% endif %}  
{% endfor %}
