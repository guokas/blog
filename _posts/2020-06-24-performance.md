---
layout: post
title: "Performance"
date: 2020-06-24 09:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Performance'
navi-order: 'a1-8'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: How to improve your website's performance, how to make your website more flexible, and portable.
excerpt: How to improve your website's performance, how to make your website more flexible, and portable.
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


<!--item-->
<div>
  <span style="color: green;">In this article:</span>
  <ul>
    {% assign posts =site.posts | sort: 'navi-order' %}
    {% for post in posts %}
      {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1-8-1" or
              post.navi-order == "a1-8-2" or 
              post.navi-order == "a1-8-3" or 
              post.navi-order == "a1-8-4"%}
                  <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>
</div>
<!--item-->
