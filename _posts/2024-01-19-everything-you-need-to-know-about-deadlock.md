---
layout: post
title: "Everything You Need To Know About Deadlock"
date: 2024-01-19 10:04:01+0800
categories:
  - MSSQL
tags:
navi-enable-mssql: true
navi-name: 'Everything You Need To Know About Deadlock'
navi-order: 'a1-1-3'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: 
excerpt: 
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

In the Database, deadlock can be one of the most headache issues. As it presents maybe stochastic, usually accompanyed with high volumns or concurrencies on DML operations. You need to identify what locks granted caused deadlocks and how to mitigate them.

Imaging in an application, which containes logics of dizziness bussiness. It's impractical to tidy all the SQL operations to make competitive resources consecutive. You need to detect deadlocks in the SQL Server side.


# Detect Blocking Operations
Please be noted, The blocked candidates don't always led to deadlock irresistibly. However, most of blocked operations will resume to continuous execution. You could gain blocked operations from the `SYS.DM_EXEC_REQUESTS` table.

```SQL
  SELECT 
      session_id, 
      start_time, 
      [status], 
      command, 
      blocking_session_id, 
      wait_type, 
      wait_time, 
      open_transaction_count, 
      transaction_id, 
      total_elapsed_time, 
      Definition = CAST(text AS VARCHAR(MAX))
  FROM 
      SYS.DM_EXEC_REQUESTS 
  CROSS APPLY 
      sys.dm_exec_sql_text (sql_handle)
  where wait_time != 0;
```

The above snippet will list out all active sessions blocking in a real-time. blocking is caused by resource competition. These granted locks can be viewed though `sys.dm_tran_locks ` table.

```SQL
  SELECT * 
  FROM sys.dm_tran_locks 
  WHERE resource_database_id = DB_ID(N'your_database') 
  AND resource_associated_entity_id = OBJECT_ID(N'schema.your_table');
```

<div class="imgcenter" markdown="1">
![Alt][1]*DM_EXEC_REQUESTS*
</div>

# Capture deadlocks in SQL Server
The Deadlock graphs can be saved as XML files by using SQL Server Profiler, according to Microsoft Save deadlock graphs(SQL Server Profiler) article. 

<div class="imgcenter" markdown="1">
![Alt][2]*New Trace*
</div>
<div class="imgcenter" markdown="1">
![Alt][3]*Save to File*
</div>
<div class="imgcenter" markdown="1">
![Alt][4]*Show All Event*
</div>
<div class="imgcenter" markdown="1">
![Alt][5]*Choose deadlock*
</div>
<div class="imgcenter" markdown="1">
![Alt][7]*Event Extraction Settings*
</div>
<div class="imgcenter" markdown="1">
![Alt][6]*Deadlock XML*
</div>

The following is an example of deadlock file. 
Open with Microsoft Sql Server Management Studio ：
<div class="imgcenter" markdown="1">
![Alt][8]*daedlock xdl*
</div>

Open with Notepad：
```html
<deadlock-list>
 <deadlock victim="process80b632ca8">
  <process-list>
   <process id="process80b632ca8" taskpriority="0" logused="280" waitresource="KEY: 5:72057594043564032 (a21f597b58d4)" waittime="4245" ownerId="91294" transactionname="INSERT" lasttranstarted="2024-01-18T09:06:28.080" XDES="0xf1b08c460" lockMode="RangeS-S" schedulerid="3" kpid="608" status="suspended" spid="109" sbid="0" ecid="0" priority="0" trancount="2" lastbatchstarted="2024-01-18T09:06:28.080" lastbatchcompleted="2024-01-18T09:06:28.077" lastattention="1900-01-01T00:00:00.077" clientapp="Core .Net SqlClient Data Provider" hostname="DESKTOP-Q52QT2Q" hostpid="23768" loginname="sa" isolationlevel="read committed (2)" xactid="91294" currentdb="5" currentdbname="PartsFeedDB" lockTimeout="4294967295" clientoption1="671088672" clientoption2="128056">
    <executionStack>
     <frame procname="adhoc" line="2" stmtstart="212" stmtend="558" sqlhandle="0x020000006262f618e4fefc26347c853745e4b0da99e0d3000000000000000000000000000000000000000000">unknown     </frame>
     <frame procname="unknown" line="1" sqlhandle="0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000">unknown     </frame>
    </executionStack>
    <inputbuf>
(@SendingRecordsId uniqueidentifier,@SentBy nvarchar(4000),@SentDate datetime,@Status nvarchar(4000))
      insert into [dbo].[sending_status] ([sending_records_id],
      [status],
      [sent_date],
      [sent_by])
      values(@SendingRecordsId,
      @Status,
      @SentDate,
      @SentBy);
        </inputbuf>
   </process>
   <process id="process80b683468" taskpriority="0" logused="284" waitresource="KEY: 5:72057594043695104 (15e82cdcfcb9)" waittime="4206" ownerId="91293" transactionname="INSERT" lasttranstarted="2024-01-18T09:06:28.080" XDES="0xf1cb3c460" lockMode="RangeS-S" schedulerid="11" kpid="800" status="suspended" spid="123" sbid="0" ecid="0" priority="0" trancount="2" lastbatchstarted="2024-01-18T09:06:28.080" lastbatchcompleted="2024-01-18T09:06:28.077" lastattention="1900-01-01T00:00:00.077" clientapp="Core .Net SqlClient Data Provider" hostname="DESKTOP-Q52QT2Q" hostpid="23768" loginname="sa" isolationlevel="read committed (2)" xactid="91293" currentdb="5" currentdbname="PartsFeedDB" lockTimeout="4294967295" clientoption1="671088672" clientoption2="128056">
    <executionStack>
     <frame procname="adhoc" line="2" stmtstart="264" stmtend="678" sqlhandle="0x02000000a95ca716ec86845754f47f2c428b495783198ffa0000000000000000000000000000000000000000">unknown     </frame>
     <frame procname="unknown" line="1" sqlhandle="0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000">unknown     </frame>
    </executionStack>
    <inputbuf>
(@PartsAltId uniqueidentifier,@PartsOemId uniqueidentifier,@SendingRecordsId uniqueidentifier,@SubstitutionFlag nvarchar(4000))
      insert into [dbo].[sending_parts] ([sending_records_id],
      [parts_oem_id],
      [parts_alt_id],
      [substitution_flag])
      values(@SendingRecordsId,
      @PartsOemId,
      @PartsAltId,
      @SubstitutionFlag);
        </inputbuf>
   </process>
  </process-list>
  <resource-list>
   <keylock hobtid="72057594043564032" dbid="5" objectname="PartsFeedDB.dbo.sending_parts" indexname="PK_sending_parts" id="lockf1e29ed00" mode="X" associatedObjectId="72057594043564032">
    <owner-list>
     <owner id="process80b683468" mode="X"/>
    </owner-list>
    <waiter-list>
     <waiter id="process80b632ca8" mode="RangeS-S" requestType="wait"/>
    </waiter-list>
   </keylock>
   <keylock hobtid="72057594043695104" dbid="5" objectname="PartsFeedDB.dbo.sending_status" indexname="PK_sending_status" id="lockf21faeb80" mode="X" associatedObjectId="72057594043695104">
    <owner-list>
     <owner id="process80b632ca8" mode="X"/>
    </owner-list>
    <waiter-list>
     <waiter id="process80b683468" mode="RangeS-S" requestType="wait"/>
    </waiter-list>
   </keylock>
  </resource-list>
 </deadlock>
</deadlock-list>
```

You know the sql text executed, but unable to know what variable values. As these values will be disposed after out of scope. So you unable to know the values of @SendingRecordsId, @PartsOemId, @PartsAltId, @SubstitutionFlag.

<blockquote>
<h2>Note:</h2>
Deadlock report can assist you to localte the mainsprings, But that is all. Sometimes, it's still not able to clean the mist behind the deadlock. Capture more deadlock in xdl files, then check if existed deadlocks of select statement accompanyed with update,insert,delete statements. probably that will be a pivotal of solving. 
</blockquote>

# Foreign Keys may cause deadlock in high concurrency
When two tables are related though foreign keys, any modifies on primary table will effects the referenced table. As the SQL Server need to keep the integrity of foreign keys, these actions will cause `RangeS-S` lock on the referenced table, which will increase probabilities of deadlocks in resouce's shared and exclusive lock granting scenarios.

Take a look at [Blocking on Foreign Keys in SQL Server][9] for more details.

# Query Views will acquire locks on behind tables
View is the part that easily get ignored during analyses. As Sql Server Views are created with select statement, Everything you select against a view, it will fire proper locks on behind tables. They need to be handled delicacy as well. 


<div class="imgcenter" markdown="1">
![Alt][10]*A horrid design for high concurrency*
</div>
As you can see from the above sql server diagram, the `sending_records` table has two referenced table on `sending_parts` and `sending_status` over foreign keys. In high volumn concurrency operation, it will easily cause deadlocks, as changes on `sending_records` will fire the foreign key integrity check on other two tables. It get even higher probabilities of encountered deadlock, if meanwhile another session is executing select against `sending_records`, `sending_parts`, or `sending_status`. 

Deadlock is also affected by isolation level, In SQL Server, the isolation level is `Read-Commited` by defaut. You cannot assume the above FK design will cause the deadlock 100 percent, it always related to factors such as: server performance, isolation level, concurrency volumn etc...


# Properly to use the WITH (NOLOCK) hint
The NOLOCK hint allows SQL to read data from tables by ignoring any locks and therefore not get blocked by other processes. This can improve query performance by removing the blocks, but introduces the possibility of dirty reads.

When comes to dealing with deadlocks, it is folly to put NOLOCK hint in every possible statements. Only put NOLOCK hint in necessary place.

Locks are used to maintain the data's integrity, so the NOLOCK indicator may read dirty data out. You should mitigate the use of NOLOCK hint, and use them over necessary place, and allowed with certain dirty toleracy. Otherwise, you should to refactor your database structures.



[1]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-a.png
[2]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-b.png
[3]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-c.png
[4]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-d.png
[5]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-e.png
[6]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-f.png
[7]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-g.png
[8]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-h.png
[9]: https://decipherinfosys.wordpress.com/2007/02/01/blocking-on-foreign-keys-in-sql-server/
[10]: /public/img/2024-01-19-everything-you-need-to-know-about-deadlock-i.png
