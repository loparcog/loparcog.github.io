---
layout: page
permalink: /blog/
title: Blog
---

<div class="home">

    {%- if site.posts.size > 0 -%}
    <ul class="post-list">
        {%- for post in site.posts -%}
        <li>
        {%- assign date_format = "%a, %b %-d, %Y @ %R" -%}
        <span class="post-meta">{{ post.date | date: date_format }} {{post.time}}</span>
        <h3>
            <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
            {{ post.description }}
            </a>
        </h3>
        {%- if site.show_excerpts -%}
            {{ post.excerpt }}
        {%- endif -%}
        </li>
        {%- endfor -%}
    </ul>

    {%- endif -%}

</div>