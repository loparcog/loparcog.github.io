---
layout: default
---

<div class="home">

    <h1 class="page-heading" style="text-align: center">Happy {{ "now" | date: "%A" }}!</h1>

    {{ content }}

    {%- if site.posts.size > 0 -%}
    <h2 class="post-list-heading">{{ page.list_title | default: "Posts" }}</h2>
    <ul class="post-list">
        {%- for post in site.posts -%}
        <li>
        {%- assign date_format = "%a, %b %-d, %Y @ %R" -%}
        <span class="post-meta">{{ post.date | date: date_format }} {{post.time}}</span>
        <h3>
            <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
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