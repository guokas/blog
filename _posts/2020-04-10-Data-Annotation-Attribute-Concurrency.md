---
layout: post
title: "Data Annotation - ConcurrencyCheck Attribute"
date: 2020-04-10 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: ""
navi-order: 'a1-3-7'
description: the property that applied the ConcurrencyCheck will setup a optimistic concurrency control.
excerpt: the property that applied the ConcurrencyCheck will setup a optimistic concurrency control.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
        {% if post.navi-order == "a1" or
              post.navi-order == "a1-3"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link'></a></div>
<!--navigation bar-->

Database concurrency refers to situations in which multiple users take part in trying to modify the same data. Let us deep in this scenario in two users are trying to change the order state simultaneously. Two users booked the same order, which should never happen. How do we avoid it? One of its solutions is to apply concurrency checks. 

Entity Framework 6 and Entity Framework Core both supports **optimistic concurrency control**, which can solve the above problem. When applied the **ConcurrencyCheck** to a property, the corresponding column in the database table will use the ***where*** clause complete the **optimistic concurrency check**.

We have known that the **Timestamp** is the mechanism that applied the **optimistic concurrency check**. more details about **Timestamp** check [here][1].

let's look at the below example:
```c#
    public class Student
    {
        public int Id { set; get; }
        [ConcurrencyCheck]
        public string name { set; get; }
    }
```

In the above example, the **ConcurrencyCheck** is applied to the **name** property of the **Student** entity class. So the EF will include the **name** column in the **UPDATE** statement to check for optimistic concurrency.
```c#
using (var context = new SchoolContext()) {
    //add Student class
    context.student.Add(new Student(){name = "kobe"});
    context.SaveChanges();

    Student kobe = context.student.Where<Student>(st=>st.name.Equals("kobe")).FirstOrDefault();
    kobe.name = "kobe bryant";
    context.SaveChanges();
}
```

the above example will execute the fellowing **UPDATE** statement on `SaveChanges()`, where it includes the **name** in the **where** clause.
```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [student] SET [name] = @p0
WHERE [Id] = @p1 AND [name] = @p2;
SELECT @@ROWCOUNT;
',N'@p1 int,@p0 nvarchar(4000),@p2 nvarchar(4000)',@p1=1,@p0=N'kobe bryant',@p2=N'kobe'
```

[1]: https://voltwu.github.io/blog/csharp-ef/2020/04/09/Data-Annotation-Attribute-Timestamp/