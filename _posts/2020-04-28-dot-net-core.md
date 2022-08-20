---
layout: post
title: ".NET Core"
date: 2020-04-28 10:01:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: '.net core'
navi-order: 'a1-1'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: 
excerpt: 
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->


**.NET Core** is an ***open-source***, ***cross-platform*** framework. It's a new version of the .NET Framework(pronounced as *dot net*).

**.NET Framework** was developing in the late 1990s by Microsoft. By late **2000**, Microsoft released the first beta version of **.NET 1.0**. The latest version is **.NET 4.8** that was released on **18 April 2019** today, and it is also the **last version** of the **.NET branch**. Microsoft announced that they are going to **stop developing further .NET version** in the future. 

![Alt][1]

Today, the concept of `Write once, run everywhere.` is very popular. However, the **.NET Framework** is just permitted to run on windows. So, the **.NET Core** was born. The **.NET Core** absorbed almost all advantages of the **.NET Framework**, and it also adds a lot of new features. The **.Net Core 1.0** was released in **June 2016** by Microsoft. In **2017**, the **.NET Core 2.0** was born. In **2019**, **.NET Core 3.0** was born. Microsoft is planning to release **.NET 5** in **2020**.


![Alt][2]


Below are some of the **.NET Core** advantages:

## Cross-platform
You can run .NET Core project on Windows, Linux, and Mac, etc. 

## Open-source
.NET Core is Open-Source([link][4]).

## Supports a wide range of application types
You can build multiple application types on .NET Core. such as Desktop, Web, Mobile.

## CLI
.NET Core includes CIL(common-line interface) for development, 

## Compatibility
Compatibility with .NET Framework by using [.NET started][3].


[1]: /public/img/2020-04-28-dot-net-core-a.png
[2]: /public/img/2020-04-28-dot-net-core-b.webp
[3]: https://docs.microsoft.com/en-us/dotnet/standard/net-standard
[4]: https://source.dot.net/
