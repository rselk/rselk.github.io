---
layout: page
title: Blog
tagline: 
---
{% include JB/setup %}

<ul class="toleft">
    {% for post in site.posts limit 15 %}
    <h3 class="bloglist"><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a><small> &raquo; <span>{{ post.date | date_to_string }}</span></small></h3>
    {% endfor %}
</ul>




