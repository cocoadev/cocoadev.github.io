---
layout: page
title: All Pages
---

{% for page in site.pages %}
{% if page.layout != nil %}
{% if page.layout != 'feed' %}
 * <a href="{{ page.url | remove: '/index.html' }}">{{ page.url | remove: '/index.html' }}</a>
{% endif %}
{% endif %}
{% endfor %}

