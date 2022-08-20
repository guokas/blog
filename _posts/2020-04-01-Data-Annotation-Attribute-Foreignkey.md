---
layout: post
title: "Data Annotation - ForeignKey Attribute"
date: 2020-04-01 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: "foreignkey"
navi-order: 'a1-3-2'
description: denotes a property used as a foreign key in a relationship
excerpt: denotes a property used as a foreign key in a relationship
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

# foreign key default conventions

First,Let's look into the default conventions in [MSDN](https://docs.microsoft.com/en-us/ef/core/modeling/relationships?tabs=fluent-api%2Cfluent-api-simple-key%2Csimple-key#conventions)


>The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.
>
> * If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.
>
>* If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:
>
>   * **\<navigation property name\>\<principal key property name\>**  
>   * **\<navigation property name\>Id**  
>   * **\<principal entity name\>\<principal key property name\>**  
>   * **\<principal entity name\>Id**

As per the default convention, when we are going to define a relationship, we should define navigation properties on both ends of the relationship and a foriegn key property in the dependent entity class.

Suppose we had the following entities.

```c#
public class Classroom { 
    [Key]
    public int cid { set; get; }

    public List<Student> students{set;get;}
}
public class Student { 
    public int id { set; get; }

    public int myclassroomid{set;get;}
    public Classroom myclassroom { set; get; }
}
```

The above example is going to configure a column which named `myclassroomid` which also is the **foreign key property** in the dependent entity `Student`. This scenario matching the rule: 
> **\<navigation property name\>Id**

The navigation property name is `myclassroom`, It's not mentioned explicitly but the matching is case insensitive, so `Id` also matches `ID`,`id` etc.

![Alt][1]

# [ForeignKey] attribute

The  `ForeignKey` property can override the default convention for a foreign key property. it allows us to specify the foreign key's name casually.

`ForeignKey` attribute can be applied in three ways.

## [ForeignKey] on foreign key property in dependent entity

The `[ForeignKey]` can be applied on **a foreign key property** in the dependent entity.but it doesn't have any effects, so it's not wise to apply `[ForeignKey]` property on **a foreign key property**.

let's look at the below illustration.
```c#
public class Classroom
{
    public int id { set; get; }
    public List<Student> students { set; get; }
}
public class Student
{
    public int id { set; get; }

    [ForeignKey("mycustomclassroomid")]
    public int myclassroomid { set; get; }
    public Classroom myclassroom { set; get; }
}
```
![Alt][2]

As we can see from the screenshot. it changed nothing, This is because that the **foreign key property**(`public int myclassroomid { set; get; }`) has defined the rule explicitly, so it will ignore the `[ForeignKey]` attribute. it's unnecessary to define a `[ForeignKey]` attribtue on **a foreign key property**.


## [ForeignKey] on navigation property in dependent entity
it's recommended to define **a foreign key property** explicitly on the dependent entity, but it's not required. 
> If no **foreign key property** is found, a **shadow foreign key** property will be introduced with the name **\<navigation property name\>\<principal key property name\>** or **\<principal entity name\>\<principal key property name\>** if no navigation is present on the dependent entity.

First, let's dig into the situation which maps the rule **\<navigation property name\>\<principal key property name\>**
```c#
public class Classroom
{
    public int id { set; get; }
    public List<Student> students { set; get; }
}
public class Student
{
    public int id { set; get; }
    public Classroom myownclassroom { set; get; }
}
```
Here, we don't define a **foreign key property**. Entity Framework Core will automatically introduce a **shadow foreign key** property which named `myownclassroomid`. 

![Alt][3]

The matching rule is **\<navigation property name\>\<principal key property name\>**. 
```
navigation property : Student.myownclassroom
principal key : Classroom.id
```
<p>&nbsp;</p>
Here, we can also use the `[Foreignkey]` property to override the default convention. put it above on the navigation property `myownclassroom`.
```c#
public class Classroom
{
    public int id { set; get; }
    public List<Student> students { set; get; }
}
public class Student
{
    public int id { set; get; }
    [ForeignKey("custom_myownclassroomfk")]
    public Classroom myownclassroom { set; get; }
}
```
The above codes will generate a foreign key column which named `custom_myownclassroomfk`. 

![Alt][4]

## [ForeignKey] on navigation property in principle entity
The third situation is that it doesn't define any **foreign key properties** and navigation properties on the dependent entity.
Only define collection properties in the principle entity(no inverse navigation, and no foreign key property) . example like below:

```c#
public class Classroom
{
    public int id { set; get; }
    public List<Student> students { set; get; }
}
public class Student
{
    public int id { set; get; }
}
```
we don't define navigation properties and foreign key properties in the `Student`. a **shadow foreign key** property will be automatically introduced in this scenario, the matching rule is the **\<principal entity name\>\<principal key property name\>**. 

and the shadow foreign key column name would be the `Classroomid`.
```
principal entity : Classroom
principal key property : Classroom.id
```
![Alt][5]


<p>&nbsp;</p>

The `[ForeignKey]` can also be used on the collection reference property `Classroom.students`, example like below:
```c#
public class Classroom
{
    public int id { set; get; }
    [ForeignKey("my_custom_classroom_fk")]
    public List<Student> students { set; get; }
}
public class Student
{
    public int id { set; get; }
}
```
The foreign key property column will be changed to the `my_custom_classroom_fk`. 
![Alt][6]

# Summary
we have learned the most details about the `[ForeignKey]` annotation attribute, I personally have two recommendations.
1. Aways use explicit mapping for relationships
2. Try to avoid **FK** , use **shadow key** or **fluent API map()** instead.




[1]:/blog/blog/public/img/2020-04-01-Data-Annotation-Attribute-Foreignkey-b.png
[2]:/blog/blog/public/img/2020-04-01-Data-Annotation-Attribute-Foreignkey-a.png
[3]:/blog/blog/public/img/2020-04-01-Data-Annotation-Attribute-Foreignkey-c.png
[4]:/blog/blog/public/img/2020-04-01-Data-Annotation-Attribute-Foreignkey-d.png
[5]:/blog/blog/public/img/2020-04-01-Data-Annotation-Attribute-Foreignkey-e.png
[6]:/blog/blog/public/img/2020-04-01-Data-Annotation-Attribute-Foreignkey-f.png
