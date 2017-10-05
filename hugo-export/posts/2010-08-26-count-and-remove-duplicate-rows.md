---
title: Count and remove duplicate rows
author: Iulian
type: post
date: 2010-08-26T10:53:41+00:00
url: /2010/08/count-and-remove-duplicate-rows/
categories:
  - Sql
tags:
  - identity
  - Sql

---
Let&#8217;s consider the **Customer** table rows:

Customer:

<table style="width: 400px;" border="1" cellspacing="0" cellpadding="2">
  <tr>
    <td valign="top" width="199">
      <strong>FirstName</strong></span>
    </td>
    
    <td valign="top" width="199">
      <em>nvarchar(50)</em></span>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="199">
      <strong>LastName</strong></span>
    </td>
    
    <td valign="top" width="199">
      <em>nvarchar(50)</em></span>
    </td>
  </tr>
</table>

Some records:

<table style="width: 400px;" border="1" cellspacing="0" cellpadding="2">
  <tr>
    <td width="202">
      <strong>FirstName</strong>
    </td>
    
    <td width="196">
      <strong>LastName</strong>
    </td>
  </tr>
  
  <tr>
    <td width="202">
      AAAAA
    </td>
    
    <td width="196">
      XXXX
    </td>
  </tr>
  
  <tr>
    <td width="202">
      AAAAA
    </td>
    
    <td width="196">
      XXXX
    </td>
  </tr>
  
  <tr>
    <td width="202">
      AAAAA
    </td>
    
    <td width="196">
      XXXX
    </td>
  </tr>
  
  <tr>
    <td width="202">
      AAAAA
    </td>
    
    <td width="196">
      XXXX
    </td>
  </tr>
  
  <tr>
    <td width="202">
      AAAAA
    </td>
    
    <td width="196">
      XXXX
    </td>
  </tr>
  
  <tr>
    <td width="202">
      BBBBB
    </td>
    
    <td width="196">
      YYYY
    </td>
  </tr>
  
  <tr>
    <td width="202">
      BBBBB
    </td>
    
    <td width="196">
      YYYY
    </td>
  </tr>
  
  <tr>
    <td width="202">
      BBBBB
    </td>
    
    <td width="196">
      YYYY
    </td>
  </tr>
  
  <tr>
    <td width="202">
      CCCCC
    </td>
    
    <td width="196">
      ZZZZ
    </td>
  </tr>
  
  <tr>
    <td width="202">
      CCCCC
    </td>
    
    <td width="196">
      ZZZZ
    </td>
  </tr>
</table>

In order to find duplicate rows from your table (eg. Customer) you can use this snippet:

<pre class="lang:tsql decode:true " >SELECT FirstName, COUNT(*) TotalCount
FROM Customer
GROUP BY FirstName
HAVING COUNT(*) &gt; 1
ORDER BY COUNT(*) DESC</pre>

Following code is useful to delete duplicate records. The table must have identity column, which will be used to identify the duplicate records. For table in example I added CustomerID as Identity Column and Columns which have duplicate data are FirstName and LastName.

<pre class="lang:tsql decode:true " >ALTER TABLE Customer
ADD CustomerID int identity(1,1)
	
DELETE FROM Customer
WHERE CustomerID NOT IN
(SELECT MAX(CustomerID)
FROM Customer GROUP BY FirstName, LastName)</pre>