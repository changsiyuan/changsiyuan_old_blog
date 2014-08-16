---
layout: default
title: Welcome to inotepad
---
{% include JB/setup %}

<div class="text-post posts">

{% for post in site.posts %}

    <div class="posts-item">

        <h1><a class="post_title" href="{{ post.url }}">{{post.title}}</a></h1>

        <div class="caption rich-content">
            {{ post.content | split: '<!--more-->' | first }}
        </div>

        <div class="read-more"><a class="post_title" href="{{ post.url }}">
        不信你打开 >></a></div>

        <div class="arrow-time">
            <cite>{{ post.date | date_to_long_string }}</cite>
        </div>

    </div>

{% endfor %}

</div>
