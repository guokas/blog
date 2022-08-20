---
layout: post
title: "Docker Orchestration"
date: 2020-12-22 10:03:01+0800
categories:
  - Docker
tags:
  - Kubernetes
navi-enable-docker: true
navi-name: 'Docker Orchestration'
navi-order: 'a1-5'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: It is time to discuss the docker orchestration now. We have talked a lot about docker right now, and it is time ready for production. Let us think about a scenario that you managed a microservice with tens, hundreds, or thousands of container instances. It is rough to deploy and manage these containers, and even it is impossible by manually. Here, the orchestration comes into play, with orchestration all of these becomes easier and convenient. Orchestration offers you a reliable way to deploy, scale, network, volume and manage, etc.
excerpt: It is time to discuss the docker orchestration now. We have talked a lot about docker right now, and it is time ready for production. Let us think about a scenario that you managed a microservice with tens, hundreds, or thousands of container instances. It is rough to deploy and manage these containers, and even it is impossible by manually. Here, the orchestration comes into play, with orchestration all of these becomes easier and convenient. Orchestration offers you a reliable way to deploy, scale, network, volume and manage, etc.
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
It is time to discuss the docker orchestration now. We have talked a lot about docker, and it is time ready for production. Let us think about a scenario that you managed a microservice with tens, hundreds, or thousands of container instances. It is rough to deploy and manage these containers, and even it is impossible by manually. And hence, the orchestration comes into play, with orchestration all of these becomes easier and convenient. Orchestration offers you a reliable way to deploy, scale, network, volume and manage, etc.

At present, the most popular orchestration for containerization is [Kubernetes][2], which is provided by Google. Docker also gives its own orchestration, which is [Docker Swarm][3]. Kubernetes is more complex, features, and hard to learn than Docker Swarm. If you want to get to know more about Kubernetes, you can take a look at [Learn Kubernetes Basics][1].

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
      {% if post.navi-order == "a1-5-1" or
            post.navi-order == "a1-5-2"
       %}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->

[1]: https://kubernetes.io/docs/tutorials/kubernetes-basics/
[2]: https://kubernetes.io/
[3]: https://docs.docker.com/get-started/swarm-deploy/