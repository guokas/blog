---
layout: post
title: "Enable Docker Support On Visual Studio"
date: 2020-07-20 09:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
  - Visual Studio
navi-enable-csharpaspnetcore: true
navi-name: 'Enable Docker Support On Visual Studio'
navi-order: 'a1-9-1-2'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: It is significant that knowing how to use Visual Studio Docker Tools for dotnet developers. You've learned how to deploy your dotnet core apps in docker in the preceding article. Let's reveal the docker tools on Visual Studio editor before we dive into further. This blog aims to illustrate those docker tools, but wouldn't get into the details. 
excerpt: It is significant that knowing how to use Visual Studio Docker Tools for dotnet developers. You've learned how to deploy your dotnet core apps in docker in the preceding article. Let's reveal the docker tools on Visual Studio editor before we dive into further. This blog aims to illustrate those docker tools, but wouldn't get into the details. 
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

It is significant that knowing how to use Visual Studio Docker Tools for dotnet developers. You've learned how to deploy your dotnet core apps in docker in the preceding article. Let's reveal the docker tools on Visual Studio editor before we dive into further. This blog aims to illustrate those docker tools, but wouldn't get into the details. 

For now, the latest version of the visual studio is **2019**, which has supported two principal features of docker(**Docker Support** and **Orchestrator Support**). 

In [section1][3], we build our app through CIL(using `dotnet` command) and then publish it as well as in the CIL environment(using `docker` command). Visual Studio 2019 assembled those features in UI, and hence we can finish these steps through Visual Studio, instead of CIL.

You can add Docker support on either a new project or an existing project. 

<div class="imgcenter" markdown="1">
![Alt][2]*Adding Docker support on a creating project(from [microsoft doc][4])*
</div>

<div class="imgcenter" markdown="1">
![Alt][1]*Adding Docker Support on an existing project(from [microsoft doc][4])*
</div>

If you select **Docker Support**, then Visual Studio will generate a Dockerfile under your project. You can use the Dockerfile to build and manage your images and containers, as the [previous article][3] did. 

If you want to compose a multi-container solution, then select the **Container Orchestrator Support** option, which enables you to debug and run a group of containers at the same time. Visual Studio will create a new project when you clicked the **Container Orchestrator Support** option. That is because orchestrators are tools to manage, scale, and maintain containerized applications, so Visual Studio made a third isolated project to finish this management.

<div class="imgcenter" markdown="1">
![Alt][5]*Adding a Docker Compose support on an existing project(from [microsoft doc][4])*
</div>

You can add multiple projects into a single docker-compose. When you add a new project into a docker-compose, then the docker-compose will reference that project's Dockerfile into the *docker-compose.yml* file. When you launch the docker-compose, all the related Dockerfile will be launched at the same time.

[1]: /public/img/2020-07-20-enable-docker-support-on-visual-studio-a.png
[2]: /public/img/2020-07-20-enable-docker-support-on-visual-studio-b.png
[3]: /csharp-asp.net%20core/2020/07/13/warm-up-build-asp-net-core-console-docker-app/
[4]: https://docs.microsoft.com/en-us/visualstudio/containers/overview?view=vs-2019
[5]: /public/img/2020-07-20-enable-docker-support-on-visual-studio-c.png