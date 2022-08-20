---
layout: post
title: "Data Annotations Attributes"
date: 2020-03-29 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: "annotations"
navi-order: 'a1-3'
description: Data annotations attributes help programmers easly specify database-related information.
excerpt: Data annotations attributes help programmers easly specify database-related information.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef and 
          post.navi-order == "a1" %}
        <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
    {% endif %}
  {% endfor %}
<a class='navi-link'></a></div>
<!--navigation bar-->

Configuration enables you to override EF Core's default behaviour. Configuration can be applied in two ways, using the **Fluent API**, and through **DataAnnotation attributes**.  we are going to dig into **Data Annotations** in the next few articles.

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
      {% if post.navi-order == "a1-3-1" or
            post.navi-order == "a1-3-2" or 
            post.navi-order == "a1-3-3" or 
            post.navi-order == "a1-3-4" or
            post.navi-order == "a1-3-5" or 
            post.navi-order == "a1-3-6" or 
            post.navi-order == "a1-3-7"%}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->