---
layout: post
title: "ASP.NET Core Tutorial"
date: 2020-04-27 10:01:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'asp.net core tutorial'
navi-order: a1
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: ASP.NET Core is a new version of Microsoft, it's a open-source web framework which can be run on Windows, Mac, Or Linux.
excerpt: ASP.NET Core is a new version of Microsoft, it's a open-source web framework which can be run on Windows, Mac, Or Linux.
---
<!--navigation bar-->
<div class='navi-link-container'>
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->


**ASP.NET Core** is a **free**, **open-source**([link][1]), and **cross-platform** web framework. You can run your Asp.Net Core web app on Windows, Linux, and Mac, etc..


This article follows the latest release of the **.NET CORE**. The purpose of this paper is to study and share. Let's explore together!

<!--
https://www.tutorialsteacher.com/core
1. ASP.NET Core and ASP.NET
2. ASP.NET Core runtime illustration
3. 
-->
<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
      {% if post.navi-order == "a1-1" or
            post.navi-order == "a1-2" or 
            post.navi-order == "a1-3" or 
            post.navi-order == "a1-4" or 
            post.navi-order == "a1-5" or 
            post.navi-order == "a1-6" or 
            post.navi-order == "a1-7" or
            post.navi-order == "a1-8" or
            post.navi-order == "a1-9" %}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->

[1]: https://github.com/dotnet/aspnetcore

