---
title: Identify Table Fragmentation
author: Iulian
type: post
date: 2012-06-19T11:39:09+00:00
url: /2012/06/identify-table-fragmentation/
categories:
  - Sql

---
Note to myself: <a href="http://databasescripts.com/s/11/sql-server/identify-table-fragmentation" title="source" target="_blank">source</a> 

<pre class="lang:tsql decode:true " >--Script to identify table fragmentation
--Declare variables
 
DECLARE
@ID int,
@IndexID int,
@IndexName varchar(128)
 
--Set the table and index to be examined
SELECT @IndexName = 'index_name' --enter name of index
SET @ID = OBJECT_ID('table_name') --enter name of table
 
--Get the Index Values
SELECT @IndexID = IndID
FROM sysindexes
WHERE id = @ID AND name = @IndexName
 
--Display the fragmentation
DBCC SHOWCONTIG (@id, @IndexID)</pre>