---
layout: post
title: "Docker Registry"
date: 2020-08-13 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Docker Registry'
navi-order: 'a1-4'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: How to build a private docker registry. what's is the docker hub repository. How to use Docker hub. Docker hub beginner tutorial. Everything you need to know about docker hub. Docker Hub is a service provided by Docker, it provides repositories, team&organization, offical images, publisher images, builds and webhook.
excerpt: How to build a private docker registry. what's is the docker hub repository. How to use Docker hub. Docker hub beginner tutorial. Everything you need to know about docker hub. Docker Hub is a service provided by Docker, it provides repositories, team&organization, offical images, publisher images, builds and webhook.
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

According to the preceding articles, you have known that what is docker registry and their responsibilities. If you don't, please take a review look at [Docker Introduction][1] and [Share your Images on docker hub][2]. Docker registry is a place where you can store images, and as well you can retrieve them back from there. The registry has two types, `public` and `private`. The `public` doesn't need any authorizations, and hence anyone can use it. On the opposite, the `private` one does need certification authorizations, and hence only authorized users can use it.

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
      {% if post.navi-order == "a1-4-1" or
            post.navi-order == "a1-4-2" or
            post.navi-order == "a1-4-3"
       %}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->

[1]: /docker/2020/07/08/getting-started/
[2]: /docker/2020/07/08/share-your-image-on-docker-hub/