---
layout: page
title: Jiri Barton's lurking ideas
tagline:
---
{% include JB/setup %}

##recent

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

##porn of the moment

<ul class="posts">
  <li><a href="http://saltstack.org">SaltStack</a> &mdash; <span>remote execution and configuration management done right</span></li>
</ul>

##hatred of the lifetime

<ul class="posts">
</ul>
