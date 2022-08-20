---
layout: post
title: "EF Core Eager Loading"
date: 2020-03-03 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: "query"
navi-order: 'a1-2-1'
description: Eager Loading means that data is loaded as part of initial query
excerpt: Eager Loading means that data is loaded as part of initial query
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts | sort: 'navi-order' %}
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



I supposed that you had known **Eager Loading** means that related entities are loaded as part of the initial query.

This project can be found in [Github][1]

use `Include` method or `ThenInclude` method to complete an eager loading. the `Include` method can specify what navigation property you want to include.  the `ThenInclude` can allow you to continue including further levels of related entities.

```c#
public async Task<List<Team>> loadTeamsAsync()
{
    using (var context = new SchoolContext())
    {
        return await context.teams.
            Include(t => t.players).
                ThenInclude(p => p.addresses).
            ToListAsync();
    }
}
```

>**Caution**:
>
>Since version 3.0.0(.net core 3.0.0), each Include will cause an additional JOIN to be added to SQL queries produced by relational providers, whereas previous versions generated additional SQL queries. This can significantly change the performance of your queries, for better or worse. In particular, LINQ queries with an exceedingly high number of Include operators may need to be broken down into multiple separate LINQ queries in order to avoid the cartesian explosion problem.

[1]: https://github.com/voltwu/C-Sharp-Console-Application-EF-Core-Example/tree/b2d33ad3f6f19e06b20afeb68218798c7f2f9f08


