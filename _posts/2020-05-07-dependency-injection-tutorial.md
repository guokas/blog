---
layout: post
title: "Dependency Injection Tutorial"
date: 2020-05-07 10:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Dependency Injection Tutorial'
navi-order: 'a1-4'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: .NET Core is designed from scratch to support dependency injection, which is also one of the principal features. 
excerpt: .NET Core is designed from scratch to support dependency injection, which is also one of the principal features. 
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

.NET Core is designed from scratch to support dependency injection, which is also one of the principal features. 
<!-- Dependency Injection Overview -->
<!-- the mechanism behide Dependency Injection -->
<!-- Design specification -->
<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
      {% if post.navi-order == "a1-4-1" or
            post.navi-order == "a1-4-2" or 
            post.navi-order == "a1-4-3" or 
            post.navi-order == "a1-4-4"%}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->