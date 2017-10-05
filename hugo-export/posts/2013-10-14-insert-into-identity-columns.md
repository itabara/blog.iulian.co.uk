---
title: Insert into identity columns
author: Iulian
type: post
date: 2013-10-14T17:52:15+00:00
url: /2013/10/insert-into-identity-columns/
categories:
  - Sql

---
Identity field is usually used as a primary key. When you insert a new record into your table, this field automatically assign an incremented value from the previous entry. Usually, you can&#8217;t insert your own value to this field.

Let&#8217;s consider the table TableName:

<pre class="lang:tsql decode:true " >CREATE TABLE TableName
(
    Col1 int IDENTITY,
    Col2 varchar(50),
    Col3 varchar(50)
)
</pre>

<pre class="lang:tsql decode:true " >SET IDENTITY_INSERT TableName ON
INSERT INTO TableName(Col1, Col2, Col3) VALUES(3,'Val1','Val1')
INSERT INTO TableName(Col1, Col2, Col3) VALUES(4,'Val1','Val1')

SET IDENTITY_INSERT TableName OFF
</pre>

Reseed the identity field:

<pre class="lang:tsql decode:true " >DBCC checkident (TableName, RESEED, 3)</pre>