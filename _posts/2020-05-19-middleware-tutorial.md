---
layout: post
title: "Middleware Tutorial"
date: 2020-05-19 10:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Middleware Tutorial'
navi-order: 'a1-5'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: ASP.NET Core introduced a new feature called as Middleware. Middleware is a software that assembled a set of applications, which will be going through on each request. In other words, it's a layer that each incoming and outgoing request will go through it.
excerpt: ASP.NET Core introduced a new feature called as Middleware. Middleware is a software that assembled a set of applications, which will be going through on each request. In other words, it's a layer that each incoming and outgoing request will go through it.
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

ASP.NET Core introduced a new feature called as Middleware. Middleware is a software that assembled a set of applications, which will be going through on each request. In other words, it's a layer that each incoming and outgoing request will go through it.

<!-- Middleware overview -->
<!-- Built your own middleware  -->
<!-- built-in middleware(tutorial) -->
<!-- conclution -->


<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
      {% if post.navi-order == "a1-5-1" or
            post.navi-order == "a1-5-2" or 
            post.navi-order == "a1-5-3" or 
            post.navi-order == "a1-5-4"%}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->