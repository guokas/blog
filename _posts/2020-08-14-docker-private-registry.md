---
layout: post
title: "Docker private registry"
date: 2020-08-14 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Docker private registry'
navi-order: 'a1-4-2'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: As a curious programmer, you may want to build a private, unlimited registry instead of using the docker hub. You can even secure your docker registry, as well as distribute roles. This tutorial is designed from scratch to advance level, so I do hope this tutorial can help you. If you learned anything new, please give me a thumber up.
excerpt: As a curious programmer, you may want to build a private, unlimited registry instead of using the docker hub. You can even secure your docker registry, as well as distribute roles. This tutorial is designed from scratch to advance level, so I do hope this tutorial can help you.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% if post.navi-order == "a1" 
        or post.navi-order == "a1-4"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->
Docker hub indeed allows you to create private repositories, but the repositories amounts are limited unless you upgrade to a premium.

As a curious programmer, you may want to build a private, unlimited registry instead of using the docker hub. You can even secure your docker registry, as well as distribute roles. This tutorial is designed from scratch to advance level, so I do hope this tutorial can help you. If you learned anything new, please give me a thumber up.

Let us get started now!
<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
      {% if post.navi-order == "a1-4-2-1" or
            post.navi-order == "a1-4-2-2" or
            post.navi-order == "a1-4-2-3" or 
            post.navi-order == "a1-4-2-4" 
       %}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->