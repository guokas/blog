---
layout: post
title: "Execute Raw SQL"
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
navi-order: 'a1-5'
description: entity framework core supports object-oriented operations, also supports raw sql operations, query, insert, delete, update etc...
excerpt: entity framework core supports object-oriented operations, also supports raw sql operations, query, insert, delete, update etc...
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

ORM can't handle complex situations. From time to time, you may need to uplifting performance from executing raw SQL. 

Microsoft Document described how the raw SQL queries stuff work on [Raw SQL Queries][1].


Subject to the [limitations][2], here's a quote:
>There are a few limitations to be aware of when using raw SQL queries:
>
>   * The SQL query must return data for all properties of the entity type.
>   * The column names in the result set must match the column names that properties are mapped to. Note this behavior is different from EF6. EF6 ignored property to column mapping for raw SQL queries and result set column names had to match the property names.
>   * The SQL query can't contain related data. However, in many cases you can compose on top of the query using the Include operator to return related data (see Including related data).


So, **apply the raw SQL queries on Entity Framework Core still have limitations**. 

If you are looking for **an unlimited solution**, then you should use **ADO.NET**,  which allows you to return whatever data type you wanted.


You can get the `DatabaseFacade` instance from the `Context` instance by calling the `Database` attribute. Once you get the `DatabaseFacade` instance, then you can do whatever custom operation you wanted.
```c#
class SchoolContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("Server=.;Database=SchoolDb;Trusted_Connection=True;");
    }
    public DataSet executeProcedure(String procudureName, params SqlParameter[] sqlParameters)
    {
        return executeSqlCommand(procudureName, CommandType.StoredProcedure, sqlParameters);
    }
    public DataSet executeSql(String commandText, params SqlParameter[] sqlParameters)
    {
        return executeSqlCommand(commandText, CommandType.Text, sqlParameters);
    }
    private DataSet executeSqlCommand(String commandText, CommandType Commandtype, params SqlParameter[] sqlParameters)
    {
        DataSet myset = new DataSet();
        using (var command = Database.GetDbConnection().CreateCommand())
        {
            command.CommandText = commandText;
            command.CommandType = Commandtype;
            foreach (var _kv in sqlParameters)
            {
                DbParameter _dbpara = command.CreateParameter();
                _dbpara.ParameterName = _kv.ParameterName;
                _dbpara.Value = _kv.Value;
                command.Parameters.Add(_dbpara);
            }
            Database.OpenConnection();
            DbDataAdapter adapter = DbProviderFactories.GetFactory(Database.GetDbConnection()).CreateDataAdapter();
            adapter.SelectCommand = command;
            adapter.Fill(myset);
        }
        return myset;
    }
  }
}
```


[1]: https://docs.microsoft.com/en-us/ef/core/querying/raw-sql
[2]: https://docs.microsoft.com/en-us/ef/core/querying/raw-sql#limitations


