---
layout: post
title: "Entity Framework Tutorial"
date: 2020-02-28 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE Of CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: "EF"
navi-order: 'a1'
description: This is a tutorial for entity framework.
excerpt: This is an Entity Framework Tutorial
---

>Entity Framework is an object-relational mapper (O/RM) that enables .NET developers to work with a database using .NET objects. It eliminates the need for most of the data-access code that developers usually need to write.

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
      {% if post.navi-order == "a1-1" or
            post.navi-order == "a1-2" or 
            post.navi-order == "a1-3" or 
            post.navi-order == "a1-4" or 
            post.navi-order == "a1-5" or 
            post.navi-order == "a1-6" or 
            post.navi-order == "a1-7"%}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->
