---
layout: post
title: "Fluent API"
date: 2020-04-15 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: ""
navi-order: 'a1-4-1'
description: the fluent api is another data annotation implemention which applied more functions.
excerpt: the fluent api is another data annotation implemention which applied more functions.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
        {% if post.navi-order == "a1" or 
              post.navi-order == "a1-4"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link'></a></div>
<!--navigation bar-->

In Entity Framework Core, you can override the `DbContext.ModelBuilder` method of `DbContext` to use the Fluent API functionality.

```c#
class SchoolContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("Server=_server;Database=_database;Trusted_Connection=True;");
    }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Student>(entity => {
            entity.ToTable("student","dbo");
            entity.HasKey(stu=>stu.Id);
            entity.Property(e => e.Id).
                HasColumnType("int").
                HasColumnName("id").
                IsRequired();
            entity.Property(e => e.name).
                HasMaxLength(128).
                HasColumnType("nvarchar").
                HasColumnName("name");
            entity.Property(e => e.add_time).
                HasColumnType("datetime").
                HasColumnName("addtime").
                HasDefaultValueSql("getdate()").
                ValueGeneratedOnAdd();
            entity.Property(e => e.last_modify_time).
                HasColumnType("datetime").
                HasColumnName("lastmodifytime").
                HasDefaultValueSql("getdate()").
                ValueGeneratedOnAddOrUpdate();
        });
    }
}
public class Student
{
    public int Id { set; get; }
    public string name { set; get; }
    public DateTime add_time { set; get; }
    public DateTime last_modify_time { set; get; }
}
```
Fluent API provides methods for configuring variable aspects:
* Model Configuration
* Entity Configuration
* Property Configuration

# Model Configuration
You can configure on the `ModelBuilder` instance such as the default scheme, functions, ignore given entities. 

The `ModelBuilder` instance includes a lot of methods that used to break the default convention.
```c#
modelBuilder.Ignore<Student>();
modelBuilder.HasDefaultSchema("dbo");
modelBuilder.HasDbFunction(typeof(SchoolContext).GetMethod("myCustomFunction"));
//...
//other configurations
```


# Entity Configuration

**entity configuration** configure entity to table and relationship mapping such as the Primary key, Foreign Key, Index, Table etc...

```c#
modelBuilder.Entity<Student>(entity => {
    entity.ToTable("student","dbo");
    entity.HasKey(stu=>stu.Id);
    entity.HasIndex(stu=>stu.gender);
});
public class Student
{
    public int Id { set; get; }
    public string name { set; get; }
    public int gender { set; get; }
    public DateTime add_time { set; get; }
    public DateTime last_modify_time { set; get; }
}
```

# Property Configuration

**Property Configuration** configure property to column mapping such as column name, column type, default value, Foreign key, concurrency check, etc...
```c#
entity.Property(e => e.add_time).
    HasColumnType("datetime").
    HasColumnName("addtime").
    HasDefaultValueSql("getdate()").
    ValueGeneratedOnAdd();
```

You can do almost any configurations in Fluent API. The First-Code default convention provides enough mechanisms for relationship configuration. So you don't have to configure it. But if you desire to perform all configuration in Fluent API for easy maintaining purposes.

We are going to illustrate the `One-To-Many` relationship, the `One-To-One` relationship and the `Many-To-Many` relationship.

## One-To-Many

Let's look the `One-To-Many` relationship first!
```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    //a student can have lots of teachers
    modelBuilder
        .Entity<Teacher>()
        .HasOne<Student>(teh => teh.student)
        .WithMany(stu=>stu.teachers)
        .HasForeignKey(teh=>teh.studentid)
        .OnDelete(DeleteBehavior.SetNull);
}
public class Student
{
    public int Id { set; get; }
    public string name { set; get; }
    public List<Teacher> teachers { set; get; }
}
public class Teacher { 
    public int Id { set; get; }
    public string name { set; get; }
    public int studentid { set; get; }
    public Student student { set; get; }
}
```

## Many-To-Many

If a teacher can only have a student, maybe it seems too weird. The more practical relationship is that each teacher can have more than one student, and each student can also have more than one teacher. It is a Many-To-Many relationship.

The EntityFrameworkCore is still not supporting the Many-To-Many relationship without an entity class that represents the join table.

>Many-to-many relationships without an entity class to represent the join table are not yet supported. However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships. [look more][1]


So we need to define an entity class that represents the join table role, then configure twice the One-To-Many relationship.
```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    //make the studentid and teacherid both are the primary key of the table
    modelBuilder
        .Entity<StudentTeacher>()
        .HasKey(s_t=>new { s_t.studentid,s_t.teacherid});
    //configure relationship between the Student entity and StudentTeacher entity
    modelBuilder
        .Entity<StudentTeacher>()
        .HasOne<Student>(s_t => s_t.student)
        .WithMany(stu=>stu.studentTeachers)
        .HasForeignKey(s_t=>s_t.studentid);
    //configure relationship between the Teacher entity and StudentTeacher entity
    modelBuilder
        .Entity<StudentTeacher>()
        .HasOne<Teacher>(s_t => s_t.teacher)
        .WithMany(teh => teh.studentTeachers)
        .HasForeignKey(s_t=>s_t.teacherid);
}
public class Student
{
    public int Id { set; get; }
    public string name { set; get; }
    public List<StudentTeacher> studentTeachers { set; get; }
}
public class Teacher { 
    public int Id { set; get; }
    public string name { set; get; }
    public List<StudentTeacher> studentTeachers { set; get; }
}
public class StudentTeacher { 
    //for mapping StudentTeacher and student relationship
    public int studentid { set; get; }
    public Student student { set; get; }

    //for mapping StudentTeacher and teacher relationship
    public int teacherid { set; get; }
    public Teacher teacher { set; get; }
}
```

## One-To-One
Let us think about this relationship between classroom and monitor. Usually, there can only be one monitor in a classroom, and a monitor can only belong to one classroom. So It is a One-To-One relationship.
```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Classroom>()
        .HasOne<Monitor>(cls=>cls.monitor)
        .WithOne(moni=>moni.classroom)
        .HasForeignKey<Monitor>(moni=>moni.classroomid);
}
public class Classroom { 
    public int id { set; get; }
    public string name { set; get; }

    public Monitor monitor { set; get; }
}
public class Monitor { 
    public int id { set; get; }
    public string name { set; get; }

    public int classroomid { set; get; }
    public Classroom classroom { set; get; }
}
```
the database is going to generate a **unique foreign key index** on **Monitor** class which guarantees the One-To-One relationship.



[1]: https://docs.microsoft.com/en-us/ef/core/modeling/relationships?tabs=fluent-api%2Cfluent-api-simple-key%2Csimple-key#other-relationship-patterns