---
title: 'Syslog'
layout: page
permalink: /syslog.html
---

<div class="list">
{% if site.syslog.size == 0 %}
  <h2>There are no posts yet. I'll get around to write some, promise.</h2>
{% else %}
  {% for post in site.syslog %}
    {% if post.hidden != true %}
      <article class="list-item">
        <div class="list-post-date">
          <time>{{ post.date | date: "%Y-%m-%d" }}</time>
        </div>
        <div class="list-post-content syslog">
        {{ post.content }}
        </div>
      </article>
    {% endif %}
  {% endfor %}
{% endif %}
</div>