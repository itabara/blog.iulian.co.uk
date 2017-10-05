---
title: Move inactive rows to achive table via SQL statements
author: Iulian
type: post
date: 2012-06-15T12:00:16+00:00
url: /2012/06/move-inactive-rows-to-achive-table-via-sql-statements/
categories:
  - Sql

---
Check this link if you want to generate automated migrated scripts:
  
<a href="http://eyearchive.codeplex.com/" title="http://eyearchive.codeplex.com/" target="_blank">http://eyearchive.codeplex.com/</a>
  
or create your own migration scripts.

Let&#8217;s consider the two tables: 

<pre class="lang:tsql decode:true " >CREATE TABLE dbo.Tickets
    TicketID( ID int NOT NULL PRIMARY KEY CLUSTERED,
    Subject varchar(50) NOT NULL,
    CustomerID varchar(50) NOT NULL )
GO
 
CREATE TABLE dbo.TicketsInactive
    TicketID( ID int NOT NULL PRIMARY KEY CLUSTERED,
    Subject varchar(50) NOT NULL,
    CustomerID varchar(50) NOT NULL )
GO </pre>

We need a clear, transactional way to move rows from the active to the inactive tickets table.

Solution 1:

<pre class="lang:tsql decode:true " >INSERT dbo.TicketsInactive(TicketID, Subject, CustomerID)
    SELECT TicketID, Subject, CustomerID FROM (DELETE dbo.Tickets OUTPUT        DELETED.TicketID,
        DELETED.Subject,
        DELETED.CustomerIDWHERE TicketID IN ( 1, 2, 3 ) ) AS RowsToMove
 
SELECT * FROM dbo.Tickets
SELECT * FROM dbo.TicketsInactive</pre>

Solution 2:

<pre class="lang:tsql decode:true " >DELETE dbo.Tickets
          OUTPUT
                  DELETED.TicketID,
                  DELETED.Subject,
                  DELETED.CustomerID
          INTO dbo.TicketsInactive
          WHERE TicketIDIN ( 1, 2, 3 )</pre>