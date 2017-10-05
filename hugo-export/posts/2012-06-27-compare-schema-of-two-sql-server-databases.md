---
title: Compare Schema of Two SQL Server Databases
author: Iulian
type: post
date: 2012-06-27T11:29:45+00:00
url: /2012/06/compare-schema-of-two-sql-server-databases/
categories:
  - Sql

---
Note to myself: <a href="http://databasescripts.com/s/9/sql-server/compare-schema-of-two-sql-server-databases" title="source" target="_blank">source</a>

<pre class="lang:tsql decode:true " >USE Master
GO
IF EXISTS (SELECT * FROM sysobjects WHERE name = 'sp_CompareDB' and type = 'P')
DROP PROC sp_CompareDB
GO
--------------------------------------------------------------------------------------------
-- sp_CompareDB
-- 
-- The SP compares structures in 2 databases.
-- 1. Compares if all tables in one database have analog (by name) in second database
-- Tables not existing in one of databases won't be used for data comparing
-- 2. Compares if structures for tables with the same names are the same. Shows structural
-- differences like:
-- authors
-- Column Phone: in db1 - char(12), in db2 - char(14)
-- sales
-- Column Location not in db2
-- Tables, having different structures, won't be used for data comparing. However if the tables
-- contain columns of the same type and different length (like Phone in the example above) or
-- tables have compatible data types (have the same type in syscolumns - char and nchar, 
-- varchar and nvarchar etc) they will be allowed for data comparing.
--------------------------------------------------------------------------------------------
-- Parameters:
-- 1. @db1 - name of first database to compare
-- 2. @db2 - name of second database to compare
-- 3. @TabList - list of tables to compare. if empty - all tables in the databases should be
-- compared
-- 4. @NumbToShow - number of rows with differences to show. Default - 10.
-- 5. @OnlyStructure - flag, if set to 1, allows to avoid data comparing. Only structures should
-- be compared. Default - 0
-- 6. @NoTimestamp - flag, if set to 1, allows to avoid comparing of columns of timestamp
-- data type. Default - 0
-- 7. @VerboseLevel - if set to 1 allows to print querues used for data comparison
--------------------------------------------------------------------------------------------
-- Created by Viktor Gorodnichenko (c)
-- Created on: July 5, 2001
-- 060327 nbn: Changed 'Colimn' into 'column' & added "order by" to table listings.
--------------------------------------------------------------------------------------------
CREATE PROC sp_CompareDB
@db1 varchar(128),
@db2 varchar(128),
@OnlyStructure bit = 0,
@TabList varchar(8000) = '',
@NumbToShow int = 10,
@NoTimestamp bit = 0,
@VerboseLevel tinyint = 0
AS
if @OnlyStructure &lt;&gt; 0
set @OnlyStructure = 1
if @NoTimestamp &lt;&gt; 0
set @NoTimestamp = 1
if @VerboseLevel &lt;&gt; 0
set @VerboseLevel = 1
 
SET NOCOUNT ON
SET ANSI_WARNINGS ON
SET ANSI_NULLS ON
declare @sqlStr varchar(8000)
set nocount on
-- Checking if there are specified databases
declare @SrvName sysname
declare @DBName sysname
set @db1 = RTRIM(LTRIM(@db1))
set @db2 = RTRIM(LTRIM(@db2))
set @SrvName = @@SERVERNAME
if CHARINDEX('.',@db1) &gt; 0
begin
set @SrvName = LEFT(@db1,CHARINDEX('.',@db1)-1)
if not exists (select * from master.dbo.sysservers where srvname = @SrvName)
begin
print 'There is no linked server named '+@SrvName+'. End of work.'
return
end
set @DBName = RIGHT(@db1,LEN(@db1)-CHARINDEX('.',@db1))
end
else
set @DBName = @db1
exec ('declare @Name sysname select @Name=name from ['+@SrvName+'].master.dbo.sysdatabases where name = '''+@DBName+'''')
if @@rowcount = 0
begin
print 'There is no database named '+@db1+'. End of work.'
return
end
set @SrvName = @@SERVERNAME
if CHARINDEX('.',@db2) &gt; 0
begin
set @SrvName = LEFT(@db2,CHARINDEX('.',@db2)-1)
if not exists (select * from master.dbo.sysservers where srvname = @SrvName)
begin
print 'There is no linked server named '+@SrvName+'. End of work.'
return
end
set @DBName = RIGHT(@db2,LEN(@db2)-CHARINDEX('.',@db2))
end
else
set @DBName = @db2
exec ('declare @Name sysname select @Name=name from ['+@SrvName+'].master.dbo.sysdatabases where name = '''+@DBName+'''')
if @@rowcount = 0
begin
print 'There is no database named '+@db2+'. End of work.'
return
end
 
print Replicate('-',LEN(@db1)+LEN(@db2)+25)
print 'Comparing databases '+@db1+' and '+@db2
print Replicate('-',LEN(@db1)+LEN(@db2)+25)
print 'Options specified:'
print ' Compare only structures: '+CASE WHEN @OnlyStructure = 0 THEN 'No' ELSE 'Yes' END
print ' List of tables to compare: '+CASE WHEN LEN(@TabList) = 0 THEN ' All tables' ELSE @TabList END
print ' Max number of different rows in each table to show: '+LTRIM(STR(@NumbToShow))
print ' Compare timestamp columns: '+CASE WHEN @NoTimestamp = 0 THEN 'No' ELSE 'Yes' END
print ' Verbose level: '+CASE WHEN @VerboseLevel = 0 THEN 'Low' ELSE 'High' END
 
-----------------------------------------------------------------------------------------
-- Comparing structures
-----------------------------------------------------------------------------------------
print CHAR(10)+Replicate('-',36)
print 'Comparing structure of the databases'
print Replicate('-',36)
if exists (select * from tempdb.dbo.sysobjects where name like '#TabToCheck%')
drop table #TabToCheck
create table #TabToCheck (name sysname)
declare @NextCommaPos int
if len(@TabList) &gt; 0 
begin
while 1=1
begin
set @NextCommaPos = CHARINDEX(',',@TabList)
if @NextCommaPos = 0
begin
set @sqlstr = 'insert into #TabToCheck values('''+@TabList+''')'
exec (@sqlstr)
break
end
set @sqlstr = 'insert into #TabToCheck values('''+LEFT(@TabList,@NextCommaPos-1)+''')'
exec (@sqlstr)
set @TabList = RIGHT(@TabList,LEN(@TabList)-@NextCommaPos)
end
end
else -- then will check all tables
begin
exec ('insert into #TabToCheck select name from '+@db1+'.dbo.sysobjects where type = ''U''')
exec ('insert into #TabToCheck select name from '+@db2+'.dbo.sysobjects where type = ''U''')
end
-- First check if at least one table specified in @TabList exists in db1
exec ('declare @Name sysname select @Name=name from '+@db1+'.dbo.sysobjects where name in (select * from #TabToCheck)')
if @@rowcount = 0
begin
print 'No tables in '+@db1+' to check. End of work.'
return
end
-- Check if tables existing in db1 are in db2 (all tables or specified in @TabList)
if exists (select * from tempdb.dbo.sysobjects where name like '#TabNotInDB2%')
drop table #TabNotInDB2
create table #TabNotInDB2 (name sysname)
insert into #TabNotInDB2 
-- 060327 nbn: Added order by..
exec ('select name from '+@db1+'.dbo.sysobjects d1o '+
'where name in (select * from #TabToCheck) and '+
' d1o.type = ''U'' and not exists '+
'(select * from '+@db2+'.dbo.sysobjects d2o'+
' where d2o.type = ''U'' and d2o.name = d1o.name) order by name')
if @@rowcount &gt; 0
begin
print CHAR(10)+'The table(s) exist in '+@db1+', but do not exist in '+@db2+':'
select * from #TabNotInDB2 
end
delete from #TabToCheck where name in (select * from #TabNotInDB2)
drop table #TabNotInDB2
 
if exists (select * from tempdb.dbo.sysobjects where name like '#TabNotInDB1%')
drop table #TabNotInDB1
create table #TabNotInDB1 (name sysname)
insert into #TabNotInDB1 
-- 060327 nbn: Added order by..
exec ('select name from '+@db2+'.dbo.sysobjects d1o '+
'where name in (select * from #TabToCheck) and '+
' d1o.type = ''U'' and not exists '+
'(select * from '+@db1+'.dbo.sysobjects d2o'+
' where d2o.type = ''U'' and d2o.name = d1o.name) order by name')
if @@rowcount &gt; 0
begin
print CHAR(10)+'The table(s) exist in '+@db2+', but do not exist in '+@db1+':'
select * from #TabNotInDB1 
end
delete from #TabToCheck where name in (select * from #TabNotInDB1)
drop table #TabNotInDB1
-- Comparing structures of tables existing in both dbs
print CHAR(10)+'Checking if there are tables existing in both databases having structural differences ...'+CHAR(10)
if exists (select * from tempdb.dbo.sysobjects where name like '#DiffStructure%')
drop table #DiffStructure
create table #DiffStructure (name sysname)
set @sqlStr='
declare @TName1 sysname, @TName2 sysname, @CName1 sysname, @CName2 sysname,
@TypeName1 sysname, @TypeName2 sysname,
@CLen1 smallint, @CLen2 smallint, @Type1 sysname, @Type2 sysname, @PrevTName sysname
declare @DiffStructure bit
declare Diff cursor fast_forward for
select d1o.name, d2o.name, d1c.name, d2c.name, d1t.name, d2t.name,
d1c.length, d2c.length, d1c.type, d2c.type
from ('+@db1+'.dbo.sysobjects d1o 
JOIN '+@db2+'.dbo.sysobjects d2o2 ON d1o.name = d2o2.name and d1o.type = ''U'' --only tables in both dbs
and d1o.name in (select * from #TabToCheck)
JOIN '+@db1+'.dbo.syscolumns d1c ON d1o.id = d1c.id
JOIN '+@db1+'.dbo.systypes d1t ON d1c.xusertype = d1t.xusertype)
FULL JOIN ('+@db2+'.dbo.sysobjects d2o 
JOIN '+@db1+'.dbo.sysobjects d1o2 ON d1o2.name = d2o.name and d2o.type = ''U'' --only tables in both dbs
and d2o.name in (select * from #TabToCheck)
JOIN '+@db2+'.dbo.syscolumns d2c ON d2c.id = d2o.id
JOIN '+@db2+'.dbo.systypes d2t ON d2c.xusertype = d2t.xusertype)
ON d1o.name = d2o.name and d1c.name = d2c.name
WHERE (not exists 
(select * from '+@db2+'.dbo.sysobjects d2o2
JOIN '+@db2+'.dbo.syscolumns d2c2 ON d2o2.id = d2c2.id
JOIN '+@db2+'.dbo.systypes d2t2 ON d2c2.xusertype = d2t2.xusertype
where d2o2.type = ''U''
and d2o2.name = d1o.name 
and d2c2.name = d1c.name 
and d2t2.name = d1t.name
and d2c2.length = d1c.length)
OR not exists 
(select * from '+@db1+'.dbo.sysobjects d1o2
JOIN '+@db1+'.dbo.syscolumns d1c2 ON d1o2.id = d1c2.id
JOIN '+@db1+'.dbo.systypes d1t2 ON d1c2.xusertype = d1t2.xusertype
where d1o2.type = ''U''
and d1o2.name = d2o.name 
and d1c2.name = d2c.name 
and d1t2.name = d2t.name
and d1c2.length = d2c.length))
order by coalesce(d1o.name,d2o.name), d1c.name
open Diff
fetch next from Diff into @TName1, @TName2, @CName1, @CName2, @TypeName1, @TypeName2,
@CLen1, @CLen2, @Type1, @Type2
set @PrevTName = ''''
set @DiffStructure = 0
while @@fetch_status = 0
begin
if Coalesce(@TName1,@TName2) &lt;&gt; @PrevTName
begin
if @PrevTName &lt;&gt; '''' and @DiffStructure = 1
begin
insert into #DiffStructure values (@PrevTName)
set @DiffStructure = 0
end
set @PrevTName = Coalesce(@TName1,@TName2)
print @PrevTName
end
if @CName2 is null
print '' Column ''+RTRIM(@CName1)+'' not in '+@db2+'''
else
if @CName1 is null
print '' Column ''+RTRIM(@CName2)+'' not in '+@db1+'''
else
if @TypeName1 &lt;&gt; @TypeName2
print '' Column ''+RTRIM(@CName1)+'': in '+@db1+' - ''+RTRIM(@TypeName1)+'', in '+@db2+' - ''+RTRIM(@TypeName2)
else --the columns are not null(are in both dbs) and types are equal,then length are diff
print '' Column ''+RTRIM(@CName1)+'': in '+@db1+' - ''+RTRIM(@TypeName1)+''(''+
LTRIM(STR(CASE when @TypeName1=''nChar'' or @TypeName1 = ''nVarChar'' then @CLen1/2 else @CLen1 end))+
''), in '+@db2+' - ''+RTRIM(@TypeName2)+''(''+
LTRIM(STR(CASE when @TypeName1=''nChar'' or @TypeName1 = ''nVarChar'' then @CLen2/2 else @CLen2 end))+'')''
if @Type1 = @Type2
set @DiffStructure=@DiffStructure -- Do nothing. Cannot invert predicate
else
set @DiffStructure = 1
fetch next from Diff into @TName1, @TName2, @CName1, @CName2, @TypeName1, @TypeName2,
@CLen1, @CLen2, @Type1, @Type2
end
deallocate Diff
if @DiffStructure = 1
insert into #DiffStructure values (@PrevTName)
'
exec (@sqlStr)
if (select count(*) from #DiffStructure) &gt; 0
begin
print CHAR(10)+'The table(s) have the same name and different structure in the databases:'
select distinct * from #DiffStructure 
delete from #TabToCheck where name in (select * from #DiffStructure)
end
else
print CHAR(10)+'There are no tables with the same name and structural differences in the databases'+CHAR(10)+CHAR(10)
if @OnlyStructure = 1
begin
print 'The option ''Only compare structures'' was specified. End of work.'
return
end
exec ('declare @Name sysname select @Name=d1o.name
from '+@db1+'.dbo.sysobjects d1o, '+@db2+'.dbo.sysobjects d2o 
where d1o.name = d2o.name and d1o.type = ''U'' and d2o.type = ''U''
and d1o.name not in (''dtproperties'') 
and d1o.name in (select * from #TabToCheck)')
if @@rowcount = 0
begin
print 'There are no tables with the same name and structure in the databases to compare. End of work.'
return
end</pre>