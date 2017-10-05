---
title: How to reset the identity column of a SQL table
author: Iulian
type: post
date: 2010-09-29T23:44:35+00:00
url: /2010/09/how-to-reset-the-identity-column-of-a-sql-table/
categories:
  - Sql

---
To reset the value of the identity column in a Microsoft SQL table run the below query: 

<pre class="lang:tsql decode:true " >DBCC CHECKIDENT (TableName, RESEED, 0)</pre>

You will need to change TableName to the name of the table you wish to reset. This query will reset the identity column to 0, meaning the next row will have the identity 1. You can change 0 to whatever identity you wish to start from.