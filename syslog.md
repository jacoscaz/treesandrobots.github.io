---
title: 'Syslog'
layout: page
permalink: /syslog.html
---

<div class="syslog-list">
{% if site.syslog.size == 0 %}
  <h2>There are no posts yet. I'll get around to write some, promise.</h2>
{% else %}
  {% assign posts = site.syslog | sort: 'date' | reverse %}
  {% for post in posts %}
    {% if post.hidden != true %}
      {% assign id = post.url | replace: '.html','' | replace: '/syslog/','' %}
      <article class="syslog-list-item" id="{{id}}">
        <div class="syslog-list-item-header">
          <div class="syslog-list-item-title">
            <a href="/syslog.html#{{id}}">
              {{ post.title }}
            </a>
          </div>
          <div class="syslog-list-item-date">
            <time>{{ post.date | date: "%Y-%m-%d" }}</time>
          </div>
        </div>
        {% if post.tags %}
          <ul class="syslog-list-item-tags">  
            {% for tag in post.tags %}
              <li>{{ tag | xml_escape }}</li>
            {% endfor %}
          </ul>
        {% endif %}
        <div class="syslog-list-item-content">
          {{ post.content }}
        </div>
      </article>
    {% endif %}
  {% endfor %}
{% endif %}
</div>
