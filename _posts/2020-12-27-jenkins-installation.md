---
layout: post
title: "Jenkins Installation"
date: 2020-12-27 09:03:01+0800
categories:
  - Jenkins
tags:
navi-enable-jenkins: true
navi-name: 'Jenkins Installation'
navi-order: a1-1
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: According to the official document, Jenkins supports many installation ways.  You can install Jenkins on Linux, Windows, or mac. Take a look at the official document for more details. This tutorial collects some common issues during installation, also contains comprehensive installation guilds and details that everything you matter. Please be free to leave comments if you have any questions or advice.
excerpt: According to the official document, Jenkins supports many installation ways.  You can install Jenkins on Linux, Windows, or mac. Take a look at the official document for more details. This tutorial collects some common issues during installation, also contains comprehensive installation guilds and details that everything you matter. Please be free to leave comments if you have any questions or advice.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-jenkins %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->
According to the official document, Jenkins supports many installation ways.  You can install Jenkins on Linux, Windows, or mac. Take a look at the official document for more details. This tutorial collects some common issues during installation, also contains comprehensive installation guilds and details that everything you matter. Please be free to leave comments if you have any questions or advice.

<blockquote>
<b>NOTE:</b>
You can install Jenkins through Docker, it is very convenient and fast. However, If your CI/CD pipeline needs to utilize the original operating system resources, it will be difficult(such as run installed software in bash command). If you only matter the CI process, then Docker will be okay for you, because Jenkins has provided abundant plugins now. However, if you also matter the CD process, usually you need to manipulate the server, and it will be difficult when using Docker. So, use Docker or not, it all depends on you.
</blockquote>

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-jenkins %}
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
