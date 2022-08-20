---
layout: post
title: "EF Core Explicit Loading"
date: 2020-03-06 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: "query"
navi-order: 'a1-2-2'
description: Explicit Loading means that related entities are explicitly loaded from the database at a later time.
excerpt: Explicit Loading means that related entities are explicitly loaded from the database at a later time.
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



**Explicit Loading** means that related entities are explicitly loaded from the database at a later time.

This project can be found in [Github][1]


```c#
using (var context = new SchoolContext())
{
    var lake_team =  await context.teams.Where(t => t.Name == "lake").FirstOrDefaultAsync();

    //The lake.Players is a collection navigation property, which should use the Collection method.
    await context.Entry(lake_team).Collection(lake => lake.Players).LoadAsync();

    //load collection navigation property Address for each player 
    foreach (Player player in lake_team.Players) {
        await context.Entry(player).Collection(p => p.Addresses).LoadAsync();
    }
    return lake_team;
};
```

In the above example, the  `await context.Entry(lake_team).Collection(lake=>lake.Players).LoadAsync()` loads the  `Player` entities, the  `Collection` method provides access to tracking and loading information for a collection navigation property that associates this entity to a collection of another entites. The `LoadAsync` method executes the SQL query and fill up the specified reference property or collection property in the memory, all of these complete asynchronously.


As we have known. The  `Player` and `Addresses` are all collection property. one Team has many players, and one player has many homes. their structures defined as below:
```c#
class Team{
...
public List<Player> Players { set; get; }
}
class Player{
...
public List<HomeAddress> Addresses { set; get; }
}
```
The opposite of the collection property is the reference property. The collection property is the to-many relationship, but the reference property is the to-one relationship.

for example:
```c#
//class School{
//   public string SchoolName{set;get;}
//   public string loc{set;get;}
//}
class Team{
public School shool{set;get;}
}
```
The `Team` only belongs to one school, so the `school` is a reference property. 
```c#
context.Entry(team).Reference(t=>t.school).Load()
```

#  Query()
The `Collection()` or `Referece()` methods return a `CollectionEntry` object or a `ReferenceEntry` object, both objects expose change tracking information. and they are not `IQueryable` instances, So we can't continue to write further LINQ queries to filter out related data after these methods.

How we can do it? luckily, we have `Query()` method which can offer us to write further queries.
```c#
context.Entry(lake_team).Collection(lake => lake.Players).
        Query().
        Where(p=>p.Name== "Kobe Bean Bryant").
        Load();
```



[1]: https://github.com/voltwu/C-Sharp-Console-Application-EF-Core-Example/tree/921b247932ec61bf7a10d4392413e4f377652c97