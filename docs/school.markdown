---
layout: page
permalink: /notes/
title: School Notes
---

{% assign groups = site.notes | group_by: "categories" | sort: "name" %}

<div id="allcat">
<h3>All Classes</h3>
{% for group in groups %}
  {% assign gname = group.name | remove: '["' | remove: '"]' %}
  <a href="{{site.baseurl}}/notes/#{{gname|slugize}}">{{gname}}</a>
  <br/>
{% endfor %} 
</div>

<div id="archives">
{% for group in groups %}
  {% assign gname = group.name | remove: '["' | remove: '"]' %}
  <div class="archive-group">
    <div id="#{{ gname | slugize }}"></div>
    <p></p>
    <h3 class="category-head">{{ gname }}</h3>
    <a name="{{ gname | slugize }}"></a>
    {% for post in group.items %}
    <article class="archive-item">
      {%- assign date_format = "%a, %b %-d, %Y @ %R" -%}
      <span class="post-meta">{{ post.date | date: date_format }} {{post.time}}</span>
      <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>
