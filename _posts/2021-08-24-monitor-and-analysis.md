---
layout: post
title: "Monitor And Analysis"
date: 2021-08-24 10:04:01+0800
categories:
  - MSSQL
tags:
navi-enable-mssql: true
navi-name: 'Monitor And Analysis'
navi-order: 'a1-1'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: 
excerpt: 
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-mssql %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
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
    {% if post.navi-enable-mssql %}
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
