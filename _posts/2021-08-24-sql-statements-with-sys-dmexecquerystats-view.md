---
layout: post
title: "Find Slow SQL Queries With sys.dm_exec_query_stats View"
date: 2021-08-24 10:04:01+0800
categories:
  - MSSQL
tags:
navi-enable-mssql: true
navi-name: 'Find Slow SQL Queries With sys.dm_exec_query_stats View'
navi-order: 'a1-1-1'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: This article will lead you to monitor SQL's running time most simply. In a busy system, hundreds and thousands of SQLs are running, so It is necessary to find and fine-turn slower running SQLs.
excerpt: This article will lead you to monitor SQL's running time most simply. In a busy system, hundreds and thousands of SQLs are running, so It is necessary to find and fine-turn slower running SQLs.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-mssql %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

This article will lead you to monitor SQL's running time most simply. In a busy system, hundreds and thousands of SQLs are running, so It is necessary to find and fine-turn slower running SQLs.

The following SQL snippet prints the top 5 slower SQL statements. 

```sql
SELECT TOP 5
      qs.total_elapsed_time / qs.execution_count / 1000000.0 AS average_seconds,
      qs.total_elapsed_time / 1000000.0 AS total_seconds,
      qs.execution_count,
      qs.total_rows,
      qs.last_rows,
      qs.min_rows,
      qs.max_rows,
      SUBSTRING(qt.text,qs.statement_start_offset/2 +1, 
                  (CASE WHEN qs.statement_end_offset = -1 
                        THEN LEN(CONVERT(nvarchar(max), qt.text)) * 2 
                        ELSE qs.statement_end_offset end -
                              qs.statement_start_offset
                  )/2+1
                  ) AS individual_query,
      o.name AS object_name,
      DB_NAME(qt.dbid) AS database_name
FROM 
      sys.dm_exec_query_stats qs
      CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) as qt
      LEFT OUTER JOIN sys.objects o ON qt.objectid = o.object_id
WHERE 
      qt.dbid = DB_ID('Your DB Name')
ORDER BY 
      average_seconds DESC;
```
The Above snippet used the [sys.dm_exec_query_stats](1) view, which only returns cached query plans. So when a plan is removed from the cache, the corresponding rows are eliminated from this view.

The above snippet will output ten fields, `individual_query`,`object_name`, `database_name` represent the running SQL text, object name, and database name. `average_seconds`, `total_seconds`, `execution_count` help you to track the CPU cost time. `total_rows`,`last_rows`, `min_rows`,`max_rows` help you to track the number of rows returned by query.

The `qt.dbid = DB_ID('Your DB Name')` specifies the database you want to monitor. If you want to capture on all databases, you can remove this prediction condition.

# Summary
 * `sys.dm_exec_query_stats` shows available cached plans currently, instead of all plans.
 * `sys.dm_exec_query_stats` is easy to track the SQL's running time, execution counts, and returned rows, etc...

 [1]: https://docs.microsoft.com/en-us/previous-versions/sql/sql-server-2008-r2/ms189741(v=sql.105)?redirectedfrom=MSDN
