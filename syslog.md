---
title: 'Syslog'
layout: page
permalink: /syslog.html
---

<div class="list">
{% if site.syslog.size == 0 %}
  <h2>There are no posts yet. I'll get around to write some, promise.</h2>
{% else %}
  {% assign posts = site.syslog | sort: 'date' | reverse %}
  {% for post in posts %}
    {% if post.hidden != true %}
      <article class="list-item" id="{{post.url}}">
        <div class="list-post-date">
          <a href="/syslog.html#{{post.url}}"><time>{{ post.date | date: "%Y-%m-%d" }}</time></a>
        </div>
        <div class="list-post-content syslog">
          {{ post.content }}
        </div>
      </article>
    {% endif %}
  {% endfor %}
{% endif %}
</div>