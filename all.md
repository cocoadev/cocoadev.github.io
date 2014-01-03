---
layout: page
title: All
---

{% for page in site.pages %}
{% if page.layout != nil %}
{% if page.layout != 'feed' %}
 * <a href="{{ page.url | remove: '/index.html' }}">{{ page.title }}</a>
{% endif %}
{% endif %}
{% endfor %}

