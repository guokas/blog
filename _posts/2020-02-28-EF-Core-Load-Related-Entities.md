---
layout: post
title: "EF Core Load Related Entities"
date: 2020-02-28 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "Table of Contents"
toc_icon: "cog"
comments: false
navi-enable-ef: true
navi-name: "query"
navi-order: 'a1-2'
description: EF Core load related entities
excerpt: Entity Framework Core allows you to use navigation propertities in your model to load related entities...
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
      {%  if post.navi-order == "a1" %}
          <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
      {% endif %}
    {% endif %}
  {% endfor %}
<a class='navi-link'></a></div>
<!--navigation bar-->



Entity Framework Core allows you to use navigation properties in your model to load related entities. There are three common ORM patterns used to load related entities. They are Eager Loader, Explicit Loading, Lazying Loading. Those loading methods have different performance. I am going to dig into it in this article.

**Eager Loading** means that related entities are loaded from the database as part of the initial query.
**Explicit Loading** means that related entities are explicitly loaded from the database at a later time.
**Lazy Loading** means that related entities are transparently loaded from the database when the navigation property is accessed.  Lazy Loading was introduced in EF Core 2.1 as a new feature.

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts = site.posts | sort: 'navi-order'  %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
      {% if post.navi-order == "a1-2-1" or
            post.navi-order == "a1-2-2" or 
            post.navi-order == "a1-2-3" or 
            post.navi-order == "a1-2-4"%}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->

