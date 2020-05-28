---
title: "Modren C++ Posts"
permalink: /categories/modern_cpp/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Modern | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}