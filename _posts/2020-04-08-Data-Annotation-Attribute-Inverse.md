---
layout: post
title: "Data Annotation - InverseProperty Attribute"
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
navi-order: 'a1-3-4'
description: Inverse can be applied to a property to specify the inverse of a navigation property that represents the other end of the same relationship
excerpt: Inverse can be applied to a property to specify the inverse of a navigation property that represents the other end of the same relationship
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
The `Inverse` property attribute is used to denote the inverse navigation property of a relationship when the same type takes part in multipart relationships.

* The `InverseProperty` attribute is used when two entities have more than one relationship.
* For Example, in the `Student` class, you may want to track the the online class as well as the offline class.

```c#
public class Classroom
{
    public int id { set; get; }

    public List<Student> all_offlineline_students { set; get; }

    public List<Student> all_online_students { set; get; }
}
public class Student
{
    public int id { set; get; }

    public Classroom offline { set; get; }

    public Classroom online { set; get; }
}
```
The above code will make Entity Framework Core confused. The `Student` class has two navigation reference properties. The Code First convention mechanism doesn't know to use which one to establish a relationship with one of the navigation properties in the `Classroom` class. 

An expected error will be thrown out when trying to execute the command `Add-Migration` by force.

<blockquote>
<div style="color:red">Unable to determine the relationship represented by navigation property 'Classroom.all_offlineline_students' of type 'List&#60;Student&#62;'. Either manually configure the relationship, or ignore this property using the '[NotMapped]' attribute or by using 'EntityTypeBuilder.Ignore' in 'OnModelCreating'.</div>
</blockquote>

To achieve this purpose, we can apply an `InverseProperty` above on the collection properties in the `Classroom` class to specify their corresponding inverse properties in the `Student` class.

```c#
public class Classroom
{
    public int id { set; get; }
    [InverseProperty("offline")]
    public List<Student> all_offlineline_students { set; get; }
    [InverseProperty("online")]
    public List<Student> all_online_students{ set; get; }
}
public class Student
{
    public int id { set; get; }
    public Classroom offline { set; get; }
    public Classroom online { set; get; }
}
```

![Alt][1]

[1]:/blog/blog/public/img/2020-04-08-Data-Annotation-Attribute-Inverse-a.png