---
layout: page
permalink: /school/
title: School Notes
---

<div id="allcat">
<h3>All Classes</h3>
{% for category in site.categories %}
  {% capture category_name %}{{ category | first }}{% endcapture %}
  <a href="{{site.baseurl}}/school/#{{category_name|slugize}}">{{category_name}}</a>
  <br/>
{% endfor %}
</div>

<div id="archives">

{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>

    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name | slugize }}"></a>
    {% for post in site.categories[category_name] %}
    <article class="archive-item">
      <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>