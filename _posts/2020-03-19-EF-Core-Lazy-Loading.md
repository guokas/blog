---
layout: post
title: "EF Core Lazy Loading"
date: 2020-03-19 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: "query"
navi-order: 'a1-2-3'
description: Lazy loadings that the related data transparently loaded from the database when the navigation property accessed.
excerpt: Lazy loadings that the related data transparently loaded from the database when the navigation property accessed.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
      {%  if post.navi-order == "a1" or
             post.navi-order == "a1-2" %}
          <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
      {% endif %}
    {% endif %}
  {% endfor %}
<a class='navi-link'></a></div>
<!--navigation bar-->



**Lazy Loading** means that the related data is transparently loaded from the database when the navigation property is accessed.

This project can be found in [Github][1]

# How to implement lazy loading
There have several ways to implement lazy loading. the simplest way is to use `Microsoft.EntityFrameworkCore.Proxies` package. 

**First**, need to install `Microsoft.EntityFrameworkCore.Proxies` on *Nuget Package Manager*

```console
PM> Install-Package Microsoft.EntityFrameworkCore.Proxies
```

**Second**, enable lazy loading with a call to `UseLazyLoadingProxies` in your `OnConfiguring` method
```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.
        UseLazyLoadingProxies().
        UseSqlServer("Server=.;Database=SchoolDb;Trusted_Connection=True;");
}
```

**Third**, make sure all entities are public, can be inherited from, the navigation property must be virtual, contractors are forbidden that all are private.

```c#
public class Team{
  //...
  public virtual List<Player> Players { set; get; }
}
public class Player{
  //...
  public virtual List<HomeAddress> Addresses { set; get; }
}
```

**Fourth**, the conceptual of lazy loading is that the related entities will be transparently loaded from the database, so it is a separate SQL query operation. don't release your `DbContext` instance, unless you make sure you don't need it anymore.

```c#
using (var context = new SchoolContext())
{
    //the lake has a Players navigation property
    //player is not loaded yet
    var lake = context.teams.FirstOrDefault();

    //Access the Player navigation property
    //Player is loaded during the access
    //but the Addresses is still not loaded , because we haven't accessed it yet.
    var player = lake.Players.FirstOrDefault();

    //The Addresses navigation property is loaded, because  we have accessed it.
    var address = player.Addresses;
}
```

>Note:
>
>`UseLazyLoadingProxies` requires all entity types to be public, unsealed, have virtual navigation properties, and have a public or protected constructor


we have known that use the `Microsoft.EntityFrameworkCore.Proxies` can complete the lazy loading easily.

But, if we do not want to use the `Microsoft.EntityFrameworkCore.Proxies` package or any other packages. Does there exist a way to complete this? The answer is yes absolutely. The `proxies` package works by injecting the `ILazyLoading` service into an entity. so we can define our `ILazyLoading` service in our entities by ourself. 

Writing ourselves `ILazyLoading` is still depending on the `Microsoft.EntityFrameworkCore.Abstractions` package.  However, if you don't want to depend on any other packages, it's possible to avoid any depending packages by using a delegate. Here I won't go into it.

# Visual Studio Debugger itsel access the navigation property
if you are using Visual Studio, and trying to debug the program, you may found out the related entities have been loaded automatically. This is because the **Visual Studio Debugger** will try to access the navigation property, so it will trigger the automatic loading mechanism.

So, Even though the code is not run to the navigation access place, but you still can find the related entities have loaded from the debug window.

[1]:https://github.com/voltwu/C-Sharp-Console-Application-EF-Core-Example/tree/e889e3cfebe34bf823b0a69e942a629491611fde
