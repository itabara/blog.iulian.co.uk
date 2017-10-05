---
title: SQL Server performance queries
author: Iulian
type: post
date: 2012-06-25T11:32:01+00:00
url: /2012/06/sql-server-performance-queries/
categories:
  - Sql

---
If a popular query does not have an associated index it will have a large impact on performance. This script identifies the queries without an index.

Here are some links with scripts that can help.

<a href="http://databasescripts.com/s/48/sql-server/find-queries-with-missing-indexes" title="http://databasescripts.com/s/48/sql-server/find-queries-with-missing-indexes" target="_blank">1. Find queries with missing indexes</a>

<pre class="lang:tsql decode:true " >SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
    SELECT TOP 100
    st.text AS [Parent Query],
    DB_NAME(st.dbid)AS [DatabaseName],
    cp.usecounts AS [Usage Count],
    qp.query_plan
FROM sys.dm_exec_cached_plans cp
CROSS APPLY sys.dm_exec_sql_text(cp.plan_handle) st
CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle) qp
WHERE CAST(qp.query_plan AS NVARCHAR(MAX))
LIKE '%&lt;MissingIndexes&gt;%'
ORDER BY cp.usecounts DESC</pre>

<a href="http://databasescripts.com/s/46/sql-server/find-most-commonly-used-indexes" title="http://databasescripts.com/s/46/sql-server/find-most-commonly-used-indexes" target="_blank">2. Find Most Commonly Used Indexes</a>

<pre class="lang:tsql decode:true " >SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
SELECT  DB_NAME() AS DatabaseName,
    SCHEMA_NAME(o.Schema_ID) AS SchemaName,
    OBJECT_NAME(s.[object_id]) AS TableName,
    i.name AS IndexName,
    (s.user_seeks + s.user_scans + s.user_lookups) AS [Usage],
    s.user_updates,
     i.fill_factor
INTO #TempUsage
FROM sys.dm_db_index_usage_stats s
INNER JOIN sys.indexes i ON s.[object_id] = i.[object_id]
AND s.index_id = i.index_id
INNER JOIN sys.objects o ON i.object_id = O.object_id
    WHERE 1=2
 
EXEC sp_MSForEachDB 'USE [?];
 
INSERT INTO #TempUsage
SELECT TOP 25
DB_NAME() AS DatabaseName,
    SCHEMA_NAME(o.Schema_ID) AS SchemaName,
    OBJECT_NAME(s.[object_id]) AS TableName,
    i.name AS IndexName,
    (s.user_seeks + s.user_scans + s.user_lookups) AS [Usage],
    s.user_updates,
    i.fill_factor
FROM sys.dm_db_index_usage_stats s
INNER JOIN sys.indexes i ON s.[object_id] = i.[object_id]
AND s.index_id = i.index_id
INNER JOIN sys.objects o ON i.object_id = O.object_id
WHERE s.database_id = DB_ID()
AND i.name IS NOT NULL
AND OBJECTPROPERTY(s.[object_id], ''IsMsShipped'') = 0
ORDER BY [Usage] DESC'
 
SELECT TOP 10 * FROM #TempUsage ORDER BY [Usage] DESC
DROP TABLE #TempUsage</pre>

<a href="http://databasescripts.com/s/47/sql-server/find-long-running-query" title="http://databasescripts.com/s/47/sql-server/find-long-running-query" target="_blank">3. Find Long Running Queries</a>

<pre class="lang:tsql decode:true " >SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
SELECT TOP 25
CAST(qs.total_elapsed_time / 1000000.0 AS DECIMAL(28, 2)) AS [Total Duration (s)],
CAST(qs.total_worker_time * 100.0 / qs.total_elapsed_time AS DECIMAL(28, 2)) AS [% CPU],
CAST((qs.total_elapsed_time - qs.total_worker_time)* 100.0 /qs.total_elapsed_time AS DECIMAL(28, 2)) AS [% Waiting],
qs.execution_count,
CAST(qs.total_elapsed_time / 1000000.0 / qs.execution_count
AS DECIMAL(28, 2)) AS [Average Duration (s)],
SUBSTRING (qt.text,(qs.statement_start_offset/2) + 1,
((CASE WHEN qs.statement_end_offset = -1
THEN LEN(CONVERT(NVARCHAR(MAX), qt.text)) * 2
ELSE qs.statement_end_offset
END - qs.statement_start_offset)/2) + 1) AS [Individual Query],
    qt.text AS [Parent Query],
    DB_NAME(qt.dbid) AS DatabaseName,
    qp.query_plan
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) as qt
CROSS APPLY sys.dm_exec_query_plan(qs.plan_handle) qp
WHERE qs.total_elapsed_time &gt; 0
ORDER BY qs.total_elapsed_time DESC</pre>