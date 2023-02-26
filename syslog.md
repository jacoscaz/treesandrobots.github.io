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
    {% assign id = post.url | replace: '.html','' | replace: '/syslog/','' %}
      <article class="list-item" id="{{id}}">
        <div class="list-post-date">
          <a href="/syslog.html#{{id}}"><time>{{ post.date | date: "%Y-%m-%d" }}</time></a>
        </div>
        <div class="list-post-content syslog">
          {{ post.content }}
        </div>
      </article>
    {% endif %}
  {% endfor %}
{% endif %}
</div>