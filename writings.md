---
title: 'Writings'
layout: page
permalink: /writings.html
---
<div class="post-list">
{% if site.posts.size == 0 %}
  <h2>There are no posts yet. I'll get around to write some, promise.</h2>
{% else %}
  {% for post in site.posts %}
    {% if post.hidden != true %}
      <article class="post-list-item">
        <div class="post-list-item-date">
          <time>{{ post.date | date: "%Y-%m-%d" }}</time>
        </div>
        <h2 class="post-list-item-title">
          <a href="{{ post.url | prepend: site.baseurl | prepend: site.url }}">{{ post.title }}</a>
        </h2>
      </article>
    {% endif %}
  {% endfor %}
{% endif %}
</div>

