---
layout: post
title: "Tutorial: Explore EF Core Console Application"
date: 2020-02-25 10:01:01+0800
categories:
  - CSharp-EF
tags:
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
navi-enable-ef: true
navi-name: ""
navi-order: "a1-1"
description: how to run an EF(entity framework) Core Console Application, with a simple, basic demonstration.
excerpt: run an EF Core Console Application, with a simple basic demonstration.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% for post in site.posts %}
    {% if post.navi-enable-ef %}
      {%  if post.navi-order == "a1" %}
          <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
      {% endif %}
    {% endif %}
  {% endfor %}
<a class='navi-link'></a></div>
<!--navigation bar-->

# What's the difference between EF6 and EF Core?
Both EF6(Entity Framework 6) and EF Core(Entity Framework Core) are object-relational mappers. EF Core is a more modern, lightweight and extensible version of Entity Framework that has very similar capabilities and benefits to EF6. EF Core is a complete rewrite and contains many new features that not include in EF6.  [Here][1] examined the many differences.

# Install Entity Framework Core
Here you will learn how to use EF Core with .NET Core Console Application step by step. To demonstrate this, we will create a .NET Core Console Application using Visual Studio 2017 (or greater).

it's very convenient to create a .NET Core Console Application. I had already created a .NET Core Console Application.

![Alt][3]

Open `Tools` -> `NuGet Package Manager` -> `Manage Nuget Package For Solution`

![Alt][4]

Search for `Microsoft.EntityFrameworkCore.SqlServer`, then install it.

The EF Core supports many database engines. see [Database Provider][2] for details. I am using SQL Server as my database, So I installed the `Microsoft.EntityFrameworkCore.SqlServer` package. And I also need to install the *Entity Framework Core Tools* for the NuGet Package Console in Visual Studio. Search for `Microsoft.EntityFrameworkCore.Tools` in *NuGet Package For Solution* dialogue and install it.

# Create The Model
Entity Framework Core needs to have a model(Entity Data Model) to communicate with the underlying database. 

The EF model has three parts: conceptual model, storage model and mappings between the conceptual and storage model. EF Core builds the conceptual model based on your domain classes(entity classes), configurations, annotations.  EF Core builds the storage model and mappings based on the provider you use.

```c#
namespace EFCoreProject.Model
{
    //context class SchoolContext
    class SchoolContext : DbContext
    {
        public DbSet<Team> teams { set; get; }
        public DbSet<Player> players { set; get; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer("Server=.;Database=EFCoreDb;Trusted_Connection=True;");
        }
    }
    //entity class Team
    public class Team
    {
        [Key]
        [Required]
        public int id { set; get; }

        [Required(ErrorMessage = "name is required")]
        [MaxLength(20,ErrorMessage = "name is too long")]
        public string name { set; get; }

        public List<Player> players { set; get; }
    }
    //entity class Player
    public class Player 
    {
        [Key]
        [Required]
        public int id { set; get; }

        [Required(ErrorMessage = "name is required")]
        [MaxLength(20, ErrorMessage = "name is too long")]
        public string name { set; get; }
    }
}
```

As you can see in the snippet. I created two entity classes (domain classes): `Player` and `Team`. the `[Key]` annotation represents to denote this property uniquely identifies this entity. the `[Required]` annotation represents this field is required. the `[MaxLength]` annotation specifies the maximum length of array or string data. these annotations come from 
[System.ComponentModel.DataAnnotations][5] namespace.

we also created a `SchoolContext` class, which is driving from `DbContext`. `SchoolContext` has two `DbSet<TEntity>` properties, for `Team` and `Player` type, which will be mapped to `teams` and `players` tables in the underlying database. the `SchoolContext` also overrides the `OnConfiguring` method. the `OnConfiguring` method used to specify which database to use. 

# Adding A Migration
EF Core includes different methods to update or create migrations. we can execute migration command on *NuGet Package Manage Console* or *CLI(command-line interface)*.

my environment is Visual Studio, open `Tools` -> `NuGet Package Manager` -> `Package Manager Console`. enter the following command:
```
PM> add-migration CreateSchoolDB
```

This will create a `Migrations` folder in the project and create the `ModelSnapshot` file.

![Alt][6]

if want to undo this action, use command `Remove-Migration`

After creating a migration, we still need to sync the effects to the database with an `update-migration` command, as below.
```
PM> update-database
```

Each time when we made changes to the entity class, we also need to sync the effect to the database using `add-migration` and `update-database`.


# CRUD (Create, Read, Update, Delete)
Now we can use the context class to save, retrieve, modify and remove data, as shown below:

**Insert data:**
```c#
using(var context = new SchoolContext())
{
        Team team_lake = new Team()
        {
            name = "lake",
            players = new List<Player>()
            {
                new Player(){ name="Kobe"},
                new Player(){ name="O`Neal"},
                new Player(){ name="Nash"}
            }
        };
        Team team_warrior = new Team()
        {
            name = "warrior",
            players = new List<Player>
            {
                new Player(){name="Curry"},
                new Player(){name="Thompson"},
                new Player(){name="Green"}
            }
        };
        context.teams.Add(team_lake);
        context.teams.Add(team_warrior);
        context.SaveChanges();
}
```

**Update data:**

```c#
using(var context = new SchoolContext())
{                
         var Kobe = context.players.
            Where<Player>(p => p.name == "Kobe").
            First<Player>();
        Kobe.name = "Kobe Bean Bryant";
        context.SaveChanges();
}
```

**Delete data:**

```c#
using(var context = new SchoolContext())
{
        var green = context.players.
            Where<Player>(p=>p.name=="Green").
            FirstOrDefault();
        context.players.Remove(green);
        context.SaveChanges();
}
```

**Query data:**

```c#
using(var context = new SchoolContext())
{
    //with an eager loading by using Microsoft.EntityFrameworkCore.Include,it's not wise.
    //but we only use it to demonstrate this demo.
    var teams = context.teams.
        Include(team=>team.players).
        ToList();

    foreach(var team in teams)
    {
        Console.WriteLine("team name:{0}", team.name);
        foreach (var player in team.players)
        {
            Console.WriteLine("players name:{0}", player.name);
        }
    }
}
```

# Conclusion

You had learned how to run a basic EF Core program.I had put those code in Github [C# EF Core Console Application][7]. In the next chapter, I will get deeper. Because of the Coronavirus, I have much more time to write articles.^^ Have a good day! See you in the next chapter.

[1]:https://docs.microsoft.com/en-us/ef/efcore-and-ef6/index
[2]:https://docs.microsoft.com/en-us/ef/core/providers/index?tabs=vs
[3]:/blog/blog/public/img/2020-02-25-Tutorial-Explore-EF-Core-Console-Application-a.png
[4]:/blog/blog/public/img/2020-02-25-Tutorial-Explore-EF-Core-Console-Application-b.png
[5]:https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations?redirectedfrom=MSDN&view=netframework-4.8
[6]:/blog/blog/public/img/2020-02-25-Tutorial-Explore-EF-Core-Console-Application-c.png
[7]:https://github.com/voltwu/C-Sharp-Console-Application-EF-Core-Example/tree/b1e4c8a75245f4bd569b8c0b7cca586523db96f4
