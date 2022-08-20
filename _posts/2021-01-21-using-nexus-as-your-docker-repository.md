---
layout: post
title: "Using Nexus Repository Manager(NXRM) as your docker repository"
date: 2021-01-21 10:03:01+0800
categories:
  - Docker
tags:
  - Repository Manager
navi-enable-docker: true
navi-name: 'Using NXRM as your docker repository'
navi-order: 'a1-4-3'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: This article guide you to set up a Nexus Repository Manager as your docker registry from scratch, and we will cover installation, SSL, and User management in the next articles. nexus3 vs Docker Registry
excerpt: This article guide you to set up a Nexus Repository Manager as your docker registry from scratch, and we will cover installation, SSL, and User management in the next articles. nexus3 vs Docker Registry
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->


Welcome to the Nexus Repository Manager for Docker Repository tutorial. In this article, I will guide you to set up a *Nexus Repository Manager* as your docker registry from scratch, and we will cover installation, SSL, and User management in the next articles. At present, the Nexus Repository Manager has two versions [Repository Manager 2][1] and [Repository Manager 3][2]. The next articles will be demonstrating with ***Nexus Repository Manager 3***, which is also well known as **nexus3** or **Nexus Repository Manager 3(NXRM3)**.

<blockquote>
<h1>Repository Manager vs Docker Registry</h1>
A <b>Docker Registry</b> is a repository for managing Docker images, just as we learned in the previous article <a href="/docker/2020/08/14/docker-private-registry/">docker private registry</a>. However, A <b>Repository Manager</b> is not only for managing docker images, it will usually be able to manage other repository technologies such as Maven, NuGet, or Git etc.
</blockquote>


<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
      {% assign splitorder = page.navi-order | append: "-" %}
      {% assign splitnum = post.navi-order | split: splitorder %}
      {% assign stagesnumber = splitnum | split: "-" | size %}
      {% assign beginstagesnumber = splitnum | split: "a" | size %}
      {% if stagesnumber == 1 and beginstagesnumber == 1 %}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->

[1]: https://help.sonatype.com/repomanager2
[2]: https://help.sonatype.com/repomanager3
