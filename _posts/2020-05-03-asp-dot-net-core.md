---
layout: post
title: "ASP.NET Core"
date: 2020-05-03 18:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'asp.net core'
navi-order: 'a1-2'
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

**ASP.NET Core** is an ***open-source***([link][1]), ***cross-platform***, and ***free*** platform, which mainly targets run on **.NET Core**. 

**ASP.NET Core 3.x** only runs on the **.NET Core 3.x**, whereas **ASP.NET Core 2.x** runs on **.NET Core 2.x** as well as **.NET Core Framework**. more information looks [.NET standard][2].

# Run the first ASP.NET Core 3.x Application.
You can run an ASP.NET Core application on Visual Studio as well as on CLI.

## On Visual Studio

Download the Visual Studio 2019 editor. If you want to build a different .Net core application, you need to choose a different version. Bellow is the minimum required version of Visual Studio based on the target visual studio version. [more][3]

|.NET Core SDK version   	|Visual Studio version   	|
|---	|---	|
|3.1   	|Visual Studio 2019 version 16.4 or higher.   	|
|3.0   	|Visual Studio 2019 version 16.3 or higher.   	|
|2.2   	|Visual Studio 2017 version 15.9 or higher.   	|
|2.1   	|Visual Studio 2017 version 15.7 or higher.   	|

**Open Visual Studio 2019, choose create a new project**
![Alt][4]

**Choose ASP.NET Core Web Application**
![Alt][5]

**Enter your project name and choose the project location**
![Alt][6]

**Choose a version and a project template**
![Alt][7]

Here, we chose the ASP.NET Core MVC Views as our template.

Press `F5`, Start debugging.
![Alt][8]

## On CLI

You can also run this project through CLI, open a command prompt and enter the following code.
```
dotnet new webapp -o aspnetcoreapp
```

**Trust the development certificate**
```
dotnet dev-certs https --trust
```

**Run the App**
```
cd aspnetcoreapp
dotnet watch run
```

Then, open [https://localhost:5001/](https://localhost:5001/) in your browser.
![Alt][9]

The above code creates a MVC Project, However, there are two project examples, one is MVC, another is Razor. Both are a complete ASP.NET Core project, they are good projects for a newbie. [MVC booklist][10] project and [Razor Booklist][11] project.

[1]: https://github.com/dotnet/aspnetcore
[2]: https://docs.microsoft.com/en-us/dotnet/standard/net-standard
[3]: https://docs.microsoft.com/en-us/dotnet/core/install/sdk?pivots=os-windows
[4]: /public/img/2020-05-03-asp-dot-net-core-a.png
[5]: /public/img/2020-05-03-asp-dot-net-core-b.png
[6]: /public/img/2020-05-03-asp-dot-net-core-c.png
[7]: /public/img/2020-05-03-asp-dot-net-core-d.png
[8]: /public/img/2020-05-03-asp-dot-net-core-e.png
[9]: /public/img/2020-05-03-asp-dot-net-core-f.png
[10]: https://github.com/voltwu/C-Sharp-Web-Net-Core-MVC-BookListMVC
[11]: https://github.com/voltwu/C-Sharp-Web-Net-Core-MVC-BookListMVC
