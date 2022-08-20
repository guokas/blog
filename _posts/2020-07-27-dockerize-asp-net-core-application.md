---
layout: post
title: "Dockerize an ASP.NET Core application"
date: 2020-07-20 09:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Dockerize an ASP.NET Core application'
navi-order: 'a1-9-1-3'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: dockerize your asp.net core app on docker. delpoy first app on docker. how to public your app on docker container. host your asp.net app on docker. Host your web app.
excerpt: dockerize your asp.net core app on docker. delpoy first app on docker. how to public your app on docker container. host your asp.net app on docker. Host your web app.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1" or
            post.navi-order == "a1-9" or
            post.navi-order == "a1-9-1"
            %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

You may have known how to deploy an asp net core application through the preceding [two articles][3]. However, if you don't know, then you can follow this post tutorial to dockerize your application step by step.

Docker has become massively popular, and it's also pretty easy to use. It's similar to dockerize a web application as a console application, except the running commands. However, it exists many ways to host your app(the [load balance][1] and [microservices][2]). This blog arming to make the process as simple as possible, and would not dive into advance([load balance][1] and [microservices][2]).

The first essence you need is the image, and the second is to run your image as a container. There list three common ways to get your application image.
1. Get your image copy(`docker save` and `docker load` command) from memory, if you are developing in Docker Container(note: switch to the `release` mode).
2. Build from a Dockerfile.
3. Retrieve from a registry.

This tutorial examples building an image from a Dockerfile.
<script src="https://gist.github.com/voltwu/fdd6adfbaade634338ddf87de63c0f90.js"></script>

View the Dockerfile contents.
<script src="https://gist.github.com/voltwu/1069943b7f15f2939c6a4abde1909cf3.js"></script>

Build your image.
<script src="https://gist.github.com/voltwu/51299201e7e8038ae29ee5162c444b70.js"></script>

After you build your image up successfully, then you can use the following snippet to run your image as a container.
<script src="https://gist.github.com/voltwu/9c14adf1dd3697930dd310faee56e8fa.js"></script>

I hope this helped you, if you have any questions, please be free to comment.

[1]: https://en.wikipedia.org/wiki/Load_balancing_(computing)
[2]: https://en.wikipedia.org/wiki/Microservices
[3]: /csharp-asp.net%20core/2020/07/13/docker/
