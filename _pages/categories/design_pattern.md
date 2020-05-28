---
title: "Design Pattern Posts"
permalink: /categories/design_pattern/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Design_Pattern | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}
