---
layout: post
title: "From Existing Database"
date: 2020-04-24 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: ""
navi-order: 'a1-6'
description: How to generate context class and entity classes from existing database in Entity Framework Core
excerpt: How to generate context class and entity classes from existing database in Entity Framework Core
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-ef %}
        {% if post.navi-order == "a1"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link'></a></div>
<!--navigation bar-->

Here, I will illustrate how to generate Context and Entities from an existing database. As the Entity Framework Core doesn't provide a visual designer, so we need to use the `Scaffold-DbContext` command.


I have a database which already populated.

![Alt][1]

**Scaffold-DbContext Command**

Use `Scaffold-DbContext` to create models is easy. get its syntax and details by running the `get-help entityframeworkcore` or `get-help Scaffold-DbContext` command on ***Package Manager Console***:
```
Scaffold-DbContext [-Connection] <String> [-Provider] <String> [-OutputDir <String>] [-ContextDir <String>] [-Context <String>] [-Schemas <String[]>] [-Tables <String[]>] [-DataAnnotations] [-UseDatabaseNames] [-Force] [-Project <String>] [-StartupProject <String>] [<CommonParameters>]

```

In this situation, run the following code on ***NuGet package manager***:
```
PM> Scaffold-DbContext "Server=.;Database=SchoolDB;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```
it will create a **models** folder, and put the entity and context class into it.

![Alt][2]

The generated code:
```c#
public partial class SchoolDBContext : DbContext
{
    public SchoolDBContext(){}
    public SchoolDBContext(DbContextOptions<SchoolDBContext> options): base(options){}
    public virtual DbSet<Classroom> Classroom { get; set; }
    public virtual DbSet<Monitor> Monitor { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
#warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer("Server=.;Database=SchoolDB;Trusted_Connection=True;");
        }
    }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Classroom>(entity =>
        {
            entity.Property(e => e.Id).HasColumnName("id");
            entity.Property(e => e.Name).HasColumnName("name");
        });
        modelBuilder.Entity<Monitor>(entity =>
        {
            entity.HasIndex(e => e.Classroomid);
            entity.Property(e => e.Id).HasColumnName("id");
            entity.Property(e => e.Classroomid).HasColumnName("classroomid");
            entity.Property(e => e.Name).HasColumnName("name");
            entity.HasOne(d => d.Classroom)
                .WithMany(p => p.Monitor)
                .HasForeignKey(d => d.Classroomid);
        });
        OnModelCreatingPartial(modelBuilder);
    }
    partial void OnModelCreatingPartial(ModelBuilder modelBuilder);
}
public partial class Monitor
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Classroomid { get; set; }
    public virtual Classroom Classroom { get; set; }
}
public partial class Classroom
{
    public Classroom(){Monitor = new HashSet<Monitor>();}
    public int Id { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Monitor> Monitor { get; set; }
}
```

As you can see, what's applied is that the **Fluent-API**. However, if you want to use the **DataAnnotations**, then you should add the `-DataAnnotations` at the end of the command.

```
PM> Scaffold-DbContext "Server=.;Database=SchoolDB;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -DataAnnotations
```

the generated code:
```c#
public partial class SchoolDBContext : DbContext
{
    public SchoolDBContext(){}
    public SchoolDBContext(DbContextOptions<SchoolDBContext> options): base(options){}
    public virtual DbSet<Classroom> Classroom { get; set; }
    public virtual DbSet<Monitor> Monitor { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
#warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer("Server=.;Database=SchoolDB;Trusted_Connection=True;");
        }
    }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Monitor>(entity =>
        {
            entity.HasIndex(e => e.Classroomid);
        });
        OnModelCreatingPartial(modelBuilder);
    }
    partial void OnModelCreatingPartial(ModelBuilder modelBuilder);
}
public partial class Monitor
{
    [Key]
    [Column("id")]
    public int Id { get; set; }
    [Column("name")]
    public string Name { get; set; }
    [Column("classroomid")]
    public int Classroomid { get; set; }
    [ForeignKey(nameof(Classroomid))]
    [InverseProperty("Monitor")]
    public virtual Classroom Classroom { get; set; }
}
public partial class Classroom
{
    public Classroom(){Monitor = new HashSet<Monitor>();}
    [Key]
    [Column("id")]
    public int Id { get; set; }
    [Column("name")]
    public string Name { get; set; }
    [InverseProperty("Classroom")]
    public virtual ICollection<Monitor> Monitor { get; set; }
}
```
Here it is the **DataAnnotation** now!

[1]: /public/img/2020-04-26-From-Existing-Database-a.png
[2]: /public/img/2020-04-26-From-Existing-Database-b.png


