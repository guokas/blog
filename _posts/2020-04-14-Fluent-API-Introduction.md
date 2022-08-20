---
layout: post
title: "Fluent API Tutorial"
date: 2020-04-14 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: "Fluent API Tutorial"
navi-order: 'a1-4'
description: Fluent API is another alternative way of data annotation.
excerpt: Fluent API is another alternative way of data annotation.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
        {% if post.navi-order == "a1"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link'></a></div>
<!--navigation bar-->

**Fluent API** is a complementary approach that data annotation can't do.  the design pattern comes from ***Fluent API***(a.k.a [Fluent Interface][1]). the actual implement mechanism comes from [Method Chaining][2].

1. Fluent API is another way to configure domain classes over the Code First Convention and Data Annotation.
2. Fluent API is more flexible and provides developers more functionalities than Data Annotation.
3. The priority of the Entity Framework processing is ***Fluent API > Data Annotation > Default Convention***.



<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
      {% if post.navi-order == "a1-4-1" or
            post.navi-order == "a1-4-2" or 
            post.navi-order == "a1-4-3" or 
            post.navi-order == "a1-4-4" or
            post.navi-order == "a1-4-5" or 
            post.navi-order == "a1-4-6" or 
            post.navi-order == "a1-4-7"%}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->

[1]: https://en.wikipedia.org/wiki/Fluent_interface
[2]: https://en.wikipedia.org/wiki/Method_chaining