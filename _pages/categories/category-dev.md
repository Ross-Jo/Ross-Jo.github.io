---
title: "개발이야기"
layout: archive
permalink: /categories/dev
author_profile: true
---

{% assign posts = site.categories.Dev %}

{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}