---
title: 'Private: Cross table update'
author: Iulian
type: post
date: 2014-09-08T18:30:34+00:00
draft: true
private: true
url: /2014/09/cross-table-update/
categories:
  - Uncategorized

---
UPDATE U
  
SET TableA\_ColA = TableB\_ColB
  
FROM MyTableA U
       
JOIN MyTableB
           
ON TableB\_ColPK = TableA\_ColPK