---
title: "일상이야기"
layout: archive
permalink: /categories/life
author_profile: true
---

{% assign posts = site.categories.Life | sort:"date" %}

{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}