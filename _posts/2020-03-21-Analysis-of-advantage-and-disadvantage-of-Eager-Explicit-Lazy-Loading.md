---
layout: post
title: "Analysis of advantage and disadvantage of Eager Loading, Explicit Loading, Lazy Loading"
date: 2020-03-21 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: "query"
navi-order: 'a1-2-4'
description: what's the advantage and disadvantage of eager loading, explicit loading, lazy loading. c# entity framework(EF,ef)
excerpt: what's the advantages and disadvantages of eager loading, explicit loading, lazy loading. most of scenarios can fit the eager loading strategy!
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



we have known how to use Eager Loading, Explicit Loading and Lazy Loading in the previous three posts. I am going to dig into what's the advantages and disadvantages of those methods.

# Eager Loading

Eager Loading means that the related data will load from the database as the initial query.  in other words, Eager Loading can save your time from avoid executing additional SQL queries. 

both Entity Framework 6 and Entity Framework Core support eager loading of entities. if you are operating some lightweight data or fast-queries, then the eager loading would be suited for you.

Most of the scenarios can fit this Eager Loading strategy.

# Lazy Loading

the Lazy Loading is the opposite of the Eager Loading, the Lazy Loading means the related entities are loaded transparently when the navigation property is accessed. in other words, in the underlying of Entity Framework, the Lazy Loading would run separate SQL queries.

In the blowing scenario, the Lazy Loading would be a no sense option.  you have many books, each book has navigation properties like author, reviewer, editor. you are iterating books to access these navigation properties. Lazy Loading is going to degrade the performance because books are small lightweight data. for example, if you have 5 thousand books, each iteration will access 3 navigation properties, then you are going to execute 15 thousand separate SQL queries. it would be a disaster!

Actually. The Lazy Loading was added on .NET Core 2.1, it's not a wise choice for most scenarios. but if you have a member that could be costly to load, and this member is infrequently visited, for scenarios like this, it would be a good choice to use Lazy Loading. 

# Explicit Loading

Explicit loading is valid in EF 6 and EF Core both.  Explicit loading is similar to lazy loading, except that: you explicitly retrieve the related data in code; it doesn't happen automatically when you access a navigation property.  in other words, Related entities are only loaded when you say "Load!",  

So, if you are figuring how to avoid relation entities instantly load in Lazy Loading, you can disable Lazy Loading as an option, or you can use Explicit Loading.

The Explicit Loading is typically more efficient when you need the related data for all retrieved rows of the primary table. Explicit Loading would be a good practice to reduce further SQL queries.

