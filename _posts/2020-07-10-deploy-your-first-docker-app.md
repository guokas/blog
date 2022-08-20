---
layout: post
title: "Deploy Your First Docker App"
date: 2020-07-08 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Deploy Your first Docker App'
navi-order: 'a1-2'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: First Docker App will introduce a simple Docker application. I will explain how to build and manage your image, container, and apps. and how to share your app on Docker Hub.
excerpt: First Docker App will introduce a simple Docker application. I will explain how to build and manage your image, container, and apps. and how to share your image on Docker Hub.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% if post.navi-order == "a1"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
      {% if post.navi-order == "a1-2-1" or
            post.navi-order == "a1-2-2" or 
            post.navi-order == "a1-2-3" 
       %}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->