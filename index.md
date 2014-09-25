---
layout: page
title: Recent
tagline: 
---
{% include JB/setup %}

<ul class="toleft">
    {% for post in site.posts limit 15 %}
    <h3><strong><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></strong><small> &raquo; <span>{{ post.date | date_to_string }}</span></small></h3>
    {{ post.content | strip_html | truncatewords:55}}<br>
    <a href="{{ post.url }}">Read more...</a><br><br>
    {% endfor %}
</ul>




