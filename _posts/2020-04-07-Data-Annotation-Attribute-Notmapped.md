---
layout: post
title: "Data Annotation - NotMapped Attribute"
date: 2020-04-07 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: ""
navi-order: 'a1-3-3'
description: notmapped denotes a property or a class should be excluded from the database mapping
excerpt: notmapped denotes a property or a class should be excluded from the database mapping
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


As per the Code First convention, the Entity Framework will map each property which has setter and getter method to a column by default. the `NotMapped` attribute can override this default convention. Entity Framework will exclude the property which applied the `NotMapped` attribute.

Let's look at the below illustration.
```c#
public class Student
{
    public int id { set; get; }

    public string name { set; get; }

    [NotMapped]
    public int age { set; get; }
}
```

The Entity Framework doesn't map the `age` property.
![Alt][1]

<p>&nbsp;</p>
The `NotMapped` can also be permitted to apply on a Entity name. 
```c#
public class Classroom{
    public int id{set;get;}
    public List<Student> students{set;get;}
}
[NotMapped]
pubic class Student{
    public int id{set;get;}
}
```
we placed the `NotMapped` attribute above the `Student` Entity. the `Student` will not map to a table.
![Alt][2]

[1]:/blog/blog/public/img/2020-04-07-Data-Annotation-Attribute-Notmapped-a.png
[2]:/blog/blog/public/img/2020-04-07-Data-Annotation-Attribute-Notmapped-b.png