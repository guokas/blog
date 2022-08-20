---
layout: post
title: "ASP.NET Web Deployment: Command Line Deployment"
date: 2021-01-05 09:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'ASP.NET Core Web Deployment: Command Line Deployment'
navi-order: 'a1-9-2'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: Host Your Web Application by through Command Line,MSBuild, MSTest, MSDeploy,DotNet
excerpt: Host Your Web Application by through Command Line,MSBuild, MSTest, MSDeploy, DotNet
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->


Command Line is the most basic way that does the deployment process. Usually, we don't need to run any command line for the build and deployment manually. It's because Visual Studio IDE will do all of these for us in the UI. However, build and deploy through the command line is also principal to learn for DevOps. If you are unfamiliar with DevOps and Jenkins, you can take a look at [Jenkins Tutorial For DevOps][1] for more details.

This article aims to take cover a comprehensive deployment process through the command line way. Let us explore now!

<!--item-->
<div>
  <span style="color: green;">In this article:</span>
  <ul>
    {% assign posts =site.posts | sort: 'navi-order' %}
    {% for post in posts %}
      {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1-9-2-1" or
              post.navi-order == "a1-9-2-2" or 
              post.navi-order == "a1-9-2-3" or 
              post.navi-order == "a1-9-2-4"%}
                  <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>
</div>
<!--item-->

[1]: /jenkins/2020/12/23/jenkins-tutorial/