---
title: Compare Data in SQL Server Databases
author: Iulian
type: post
date: 2012-06-18T11:40:51+00:00
url: /2012/06/compare-data-in-sql-server-databases/
categories:
  - Sql

---
Note to myself: <a href="http://databasescripts.com/s/10/sql-server/compare-data-in-two-sql-server-databases" title="source" target="_blank">source</a>

<pre class="lang:tsql decode:true " >-----------------------------------------------------------------------------------------
-- Comparing data in SQL Server Databases
-----------------------------------------------------------------------------------------
-- 1. Data comparison itself. 
-- 1.1 Get information about unique keys in the tables. If there are unique keys then one of them
-- (PK is a highest priority candidate for this role) will be used to specify rows with
-- different data.
-- 1.2 Get information about all data columns in the table and form predicates that will be 
-- used to compare data.
-- 1.3 Compare data with the criteria:
-- a. if some unique keys from the table from first database do not exist in second db (only
-- for tables with a unique key)
-- b. if some unique keys from the table from second database do not exist in first db (only
-- for tables with a unique key)
-- c. if there are rows with the same values of unique keys and different data in other
-- columns (only for tables with a unique key)
-- d. if there are rows in the table from first database that don't have a twin in the 
-- table from second db
-- e. if there are rows in the table from second database that don't have a twin in the 
-- table from first db
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
-- ##CompareStr - will be used to pass comparing strings into dynamic script
-- to execute the string
  
if exists (select * from tempdb.dbo.sysobjects where name like '##CompareStr%')
drop table ##CompareStr
create table ##CompareStr (Ind int, CompareStr varchar(8000))
  
if exists (select * from tempdb.dbo.sysobjects where name like '#DiffTables%')
drop table #DiffTables
create table #DiffTables (Name sysname)
if exists (select * from tempdb.dbo.sysobjects where name like '#IdenticalTables%')
drop table #IdenticalTables
create table #IdenticalTables (Name sysname)
if exists (select * from tempdb.dbo.sysobjects where name like '#EmptyTables%')
drop table #EmptyTables
create table #EmptyTables (Name sysname)
if exists (select * from tempdb.dbo.sysobjects where name like '#NoPKTables%')
drop table #NoPKTables
create table #NoPKTables (Name sysname)
  
if exists (select * from tempdb.dbo.sysobjects where name like '#IndList1%')
truncate table #IndList1
else 
create table #IndList1 (IndId int, IndStatus int,
KeyAndStr varchar(7000), KeyCommaStr varchar(1000))
if exists (select * from tempdb.dbo.sysobjects where name like '#IndList2%')
truncate table #IndList2
else
create table #IndList2 (IndId smallint, IndStatus int,
KeyAndStr varchar(7000), KeyCommaStr varchar(1000))
  
print Replicate('-',51)
print 'Comparing data in tables with indentical structure:'
print Replicate('-',51)
--------------------------------------------------------------------------------------------
-- Cursor for all tables in dbs (or for all specified tables if parameter @TabList is passed)
--------------------------------------------------------------------------------------------
declare @SqlStrGetListOfKeys1 varchar(8000)
declare @SqlStrGetListOfKeys2 varchar(8000)
declare @SqlStrGetListOfColumns varchar(8000)
declare @SqlStrCompareUKeyTables varchar(8000)
declare @SqlStrCompareNonUKeyTables varchar(8000)
set @SqlStrGetListOfKeys1 = '
declare @sqlStr varchar(8000)
declare @ExecSqlStr varchar(8000)
declare @PrintSqlStr varchar(8000)
declare @Tab varchar(128)
declare @d1User varchar(128)
declare @d2User varchar(128)
declare @KeyAndStr varchar(8000) 
declare @KeyCommaStr varchar(8000) 
declare @AndStr varchar(8000) 
declare @Eq varchar(8000) 
declare @IndId int
declare @IndStatus int
declare @CurrIndId smallint
declare @CurrStatus int
declare @UKey sysname 
declare @Col varchar(128)
declare @LastUsedCol varchar(128)
declare @xType int
declare @Len int
declare @SelectStr varchar(8000) 
declare @ExecSql nvarchar(1000) 
declare @NotInDB1 bit 
declare @NotInDB2 bit 
declare @NotEq bit 
declare @Numb int
declare @Cnt1 int
declare @Cnt2 int
set @Numb = 0
  
declare @StrInd int
declare @i int
declare @PrintStr varchar(8000)
declare @ExecStr varchar(8000)
declare TabCur cursor for 
  
select d1o.name, d1u.name, d2u.name from '+@db1+'.dbo.sysobjects d1o, '+@db2+'.dbo.sysobjects d2o,
'+@db1+'.dbo.sysusers d1u, '+@db2+'.dbo.sysusers d2u 
where d1o.name = d2o.name and d1o.type = ''U'' and d2o.type = ''U''
and d1o.uid = d1u.uid and d2o.uid = d2u.uid 
and d1o.name not in (''dtproperties'') 
and d1o.name in (select * from #TabToCheck)
order by 1
  
open TabCur 
fetch next from TabCur into @Tab, @d1User, @d2User 
while @@fetch_status = 0 
begin 
set @Numb = @Numb + 1
print Char(13)+Char(10)+LTRIM(STR(@Numb))+''. TABLE: [''+@Tab+''] ''
  
set @ExecSql = ''SELECT @Cnt = count(*) FROM '+@db1+'.[''+@d1User+''].[''+@Tab+'']''
exec sp_executesql @ExecSql, N''@Cnt int output'', @Cnt = @Cnt1 output
print CHAR(10)+STR(@Cnt1)+'' rows in '+@db1+'''
set @ExecSql = ''SELECT @Cnt = count(*) FROM '+@db2+'.[''+@d2User+''].[''+@Tab+'']''
exec sp_executesql @ExecSql, N''@Cnt int output'', @Cnt = @Cnt2 output
print STR(@Cnt2)+'' rows in '+@db2+'''
if @Cnt1 = 0 and @Cnt2 = 0
begin
exec ('' insert into #EmptyTables values(''''[''+@Tab+'']'''')'') 
goto NextTab
end
set @KeyAndStr = '''' 
set @KeyCommaStr = '''' 
set @NotInDB1 = 0
set @NotInDB2 = 0 
set @NotEq = 0
set @KeyAndStr = '''' 
set @KeyCommaStr = '''' 
truncate table #IndList1
declare UKeys cursor fast_forward for 
select i.indid, i.status, c.name, c.xType from '+@db1+'.dbo.sysobjects o, '+@db1+'.dbo.sysindexes i, '+@db1+'.dbo.sysindexkeys k, '+@db1+'.dbo.syscolumns c 
where i.id = o.id and o.name = @Tab
and (i.status & 2)&lt;&gt;0 
and k.id = o.id and k.indid = i.indid 
and c.id = o.id and c.colid = k.colid 
order by i.indid, c.name
open UKeys 
fetch next from UKeys into @IndId, @IndStatus, @UKey, @xType
set @CurrIndId = @IndId
set @CurrStatus = @IndStatus
while @@fetch_status = 0 
begin 
if @KeyAndStr &lt;&gt; ''''
begin 
set @KeyAndStr = @KeyAndStr + '' and '' + CHAR(10) 
set @KeyCommaStr = @KeyCommaStr + '', '' 
end 
if @xType = 175 or @xType = 167 or @xType = 239 or @xType = 231 -- char, varchar, nchar, nvarchar
begin
set @KeyAndStr = @KeyAndStr + '' ISNULL(d1.[''+@UKey+''],''''!#null$'''')=ISNULL(d2.[''+@UKey+''],''''!#null$'''') ''
end
if @xType = 173 or @xType = 165 -- binary, varbinary
begin
set @KeyAndStr = @KeyAndStr +
'' CASE WHEN d1.[''+@UKey+''] is null THEN 0x4D4FFB23A49411D5BDDB00A0C906B7B4 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 0x4D4FFB23A49411D5BDDB00A0C906B7B4 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 56 or @xType = 127 or @xType = 60 or @xType = 122 -- int, 127 - bigint,60 - money, 122 - smallmoney
begin
set @KeyAndStr = @KeyAndStr + 
'' CASE WHEN d1.[''+@UKey+''] is null THEN 971428763405345098745 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 971428763405345098745 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 106 or @xType = 108 -- int, decimal, numeric
begin
set @KeyAndStr = @KeyAndStr + 
'' CASE WHEN d1.[''+@UKey+''] is null THEN 71428763405345098745098.8723 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 71428763405345098745098.8723 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 62 or @xType = 59 -- 62 - float, 59 - real
begin 
set @KeyAndStr = @KeyAndStr + 
'' CASE WHEN d1.[''+@UKey+''] is null THEN 8764589764.22708E237 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 8764589764.22708E237 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 52 or @xType = 48 or @xType = 104 -- smallint, tinyint, bit
begin
set @KeyAndStr = @KeyAndStr + '' CASE WHEN d1.[''+@UKey+''] is null THEN 99999 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 99999 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 36 -- 36 - id 
begin
set @KeyAndStr = @KeyAndStr +
'' CASE WHEN d1.[''+@UKey+''] is null''+
'' THEN CONVERT(uniqueidentifier,''''1CD827A0-744A-4866-8401-B9902CF2D4FB'''')''+
'' ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null''+
'' THEN CONVERT(uniqueidentifier,''''1CD827A0-744A-4866-8401-B9902CF2D4FB'''')''+
'' ELSE d2.[''+@UKey+''] END''
end
else if @xType = 61 or @xType = 58 -- datetime, smalldatetime
begin
set @KeyAndStr = @KeyAndStr +
'' CASE WHEN d1.[''+@UKey+''] is null THEN ''''!#null$'''' ELSE CONVERT(varchar(40),d1.[''+@UKey+''],109) END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN ''''!#null$'''' ELSE CONVERT(varchar(40),d2.[''+@UKey+''],109) END ''
end
else if @xType = 189 -- timestamp (189) 
begin
set @KeyAndStr = @KeyAndStr + '' d1.[''+@UKey+'']=d2.[''+@UKey+''] ''
end
else if @xType = 98 -- SQL_variant
begin
set @KeyAndStr = @KeyAndStr + '' ISNULL(d1.[''+@UKey+''],''''!#null$'''')=ISNULL(d2.[''+@UKey+''],''''!#null$'''') ''
end
set @KeyCommaStr = @KeyCommaStr + '' d1.''+@UKey 
fetch next from UKeys into @IndId, @IndStatus, @UKey, @xType
if @IndId &lt;&gt; @CurrIndId
begin
insert into #IndList1 values (@CurrIndId, @CurrStatus, @KeyAndStr, @KeyCommaStr)
set @CurrIndId = @IndId
set @CurrStatus = @IndStatus
set @KeyAndStr = ''''
set @KeyCommaStr = '''' 
end
end 
deallocate UKeys 
insert into #IndList1 values (@CurrIndId, @CurrStatus, @KeyAndStr, @KeyCommaStr)'
set @SqlStrGetListOfKeys2 = '
set @KeyAndStr = '''' 
set @KeyCommaStr = '''' 
truncate table #IndList2
declare UKeys cursor fast_forward for 
select i.indid, i.status, c.name, c.xType from '+@db2+'.dbo.sysobjects o, '+@db2+'.dbo.sysindexes i, '+@db2+'.dbo.sysindexkeys k, '+@db2+'.dbo.syscolumns c 
where i.id = o.id and o.name = @Tab
and (i.status & 2)&lt;&gt;0 
and k.id = o.id and k.indid = i.indid 
and c.id = o.id and c.colid = k.colid 
order by i.indid, c.name
open UKeys 
fetch next from UKeys into @IndId, @IndStatus, @UKey, @xType
set @CurrIndId = @IndId
set @CurrStatus = @IndStatus
while @@fetch_status = 0 
begin 
if @KeyAndStr &lt;&gt; ''''
begin 
set @KeyAndStr = @KeyAndStr + '' and '' + CHAR(10) 
set @KeyCommaStr = @KeyCommaStr + '', '' 
end 
if @xType = 175 or @xType = 167 or @xType = 239 or @xType = 231 -- char, varchar, nchar, nvarchar
begin
set @KeyAndStr = @KeyAndStr + '' ISNULL(d1.[''+@UKey+''],''''!#null$'''')=ISNULL(d2.[''+@UKey+''],''''!#null$'''') ''
end
if @xType = 173 or @xType = 165 -- binary, varbinary
begin
set @KeyAndStr = @KeyAndStr +
'' CASE WHEN d1.[''+@UKey+''] is null THEN 0x4D4FFB23A49411D5BDDB00A0C906B7B4 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 0x4D4FFB23A49411D5BDDB00A0C906B7B4 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 56 or @xType = 127 or @xType = 60 or @xType = 122 -- int, 127 - bigint,60 - money, 122 - smallmoney
begin
set @KeyAndStr = @KeyAndStr + 
'' CASE WHEN d1.[''+@UKey+''] is null THEN 971428763405345098745 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 971428763405345098745 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 106 or @xType = 108 -- int, decimal, numeric
begin
set @KeyAndStr = @KeyAndStr + 
'' CASE WHEN d1.[''+@UKey+''] is null THEN 71428763405345098745098.8723 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 71428763405345098745098.8723 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 62 or @xType = 59 -- 62 - float, 59 - real
begin 
set @KeyAndStr = @KeyAndStr + 
'' CASE WHEN d1.[''+@UKey+''] is null THEN 8764589764.22708E237 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 8764589764.22708E237 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 52 or @xType = 48 or @xType = 104 -- smallint, tinyint, bit
begin
set @KeyAndStr = @KeyAndStr + '' CASE WHEN d1.[''+@UKey+''] is null THEN 99999 ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN 99999 ELSE d2.[''+@UKey+''] END ''
end
else if @xType = 36 -- 36 - id 
begin
set @KeyAndStr = @KeyAndStr +
'' CASE WHEN d1.[''+@UKey+''] is null''+
'' THEN CONVERT(uniqueidentifier,''''1CD827A0-744A-4866-8401-B9902CF2D4FB'''')''+
'' ELSE d1.[''+@UKey+''] END=''+
''CASE WHEN d2.[''+@UKey+''] is null''+
'' THEN CONVERT(uniqueidentifier,''''1CD827A0-744A-4866-8401-B9902CF2D4FB'''')''+
'' ELSE d2.[''+@UKey+''] END''
end
else if @xType = 61 or @xType = 58 -- datetime, smalldatetime
begin
set @KeyAndStr = @KeyAndStr +
'' CASE WHEN d1.[''+@UKey+''] is null THEN ''''!#null$'''' ELSE CONVERT(varchar(40),d1.[''+@UKey+''],109) END=''+
''CASE WHEN d2.[''+@UKey+''] is null THEN ''''!#null$'''' ELSE CONVERT(varchar(40),d2.[''+@UKey+''],109) END ''
end
else if @xType = 189 -- timestamp (189) 
begin
set @KeyAndStr = @KeyAndStr + '' d1.[''+@UKey+'']=d2.[''+@UKey+''] ''
end
else if @xType = 98 -- SQL_variant
begin
set @KeyAndStr = @KeyAndStr + '' ISNULL(d1.[''+@UKey+''],''''!#null$'''')=ISNULL(d2.[''+@UKey+''],''''!#null$'''') ''
end
set @KeyCommaStr = @KeyCommaStr + '' d1.''+@UKey 
fetch next from UKeys into @IndId, @IndStatus, @UKey, @xType
if @IndId &lt;&gt; @CurrIndId
begin
insert into #IndList2 values (@CurrIndId, @CurrStatus, @KeyAndStr, @KeyCommaStr)
set @CurrIndId = @IndId
set @CurrStatus = @IndStatus
set @KeyAndStr = ''''
set @KeyCommaStr = '''' 
end
end 
deallocate UKeys 
insert into #IndList2 values (@CurrIndId, @CurrStatus, @KeyAndStr, @KeyCommaStr)
set @KeyCommaStr = null
  
select @KeyCommaStr=i1.KeyCommaStr from #IndList1 i1
join #IndList2 i2 on i1.KeyCommaStr = i2.KeyCommaStr
where (i1.IndStatus & 2048)&lt;&gt; 0 and (i2.IndStatus & 2048)&lt;&gt;0
  
if @KeyCommaStr is null 
set @KeyCommaStr = (select top 1 i1.KeyCommaStr from #IndList1 i1
join #IndList2 i2 on i1.KeyCommaStr = i2.KeyCommaStr)
set @KeyAndStr = (select TOP 1 KeyAndStr from #IndList1 where KeyCommaStr = @KeyCommaStr)
if @KeyCommaStr is null
set @KeyCommaStr = ''''
if @KeyAndStr is null
set @KeyAndStr = '''''
set @SqlStrGetListOfColumns = '
set @AndStr = ''''
set @StrInd = 1
declare Cols cursor local fast_forward for 
select c.name, c.xtype, c.length from '+@db1+'.dbo.sysobjects o, '+@db1+'.dbo.syscolumns c
where o.id = c.id and o.name = @Tab 
and CHARINDEX(c.name, @KeyCommaStr) = 0
open Cols 
fetch next from Cols into @Col, @xType, @len
while @@fetch_status = 0 
begin 
if @xType = 175 or @xType = 167 or @xType = 239 or @xType = 231 -- char, varchar, nchar, nvarchar
begin
set @Eq = ''ISNULL(d1.[''+@Col+''],''''!#null$'''')=ISNULL(d2.[''+@Col+''],''''!#null$'''') ''
end
if @xType = 173 or @xType = 165 -- binary, varbinary
begin
set @Eq = ''CASE WHEN d1.[''+@Col+''] is null THEN 0x4D4FFB23A49411D5BDDB00A0C906B7B4 ELSE d1.[''+@Col+''] END=''+
''CASE WHEN d2.[''+@Col+''] is null THEN 0x4D4FFB23A49411D5BDDB00A0C906B7B4 ELSE d2.[''+@Col+''] END ''
end
else if @xType = 56 or @xType = 127 or @xType = 60 or @xType = 122 -- int, 127 - bigint,60 - money, 122 - smallmoney
begin
set @Eq = ''CASE WHEN d1.[''+@Col+''] is null THEN 971428763405345098745 ELSE d1.[''+@Col+''] END=''+
''CASE WHEN d2.[''+@Col+''] is null THEN 971428763405345098745 ELSE d2.[''+@Col+''] END ''
end
else if @xType = 106 or @xType = 108 -- int, decimal, numeric
begin
set @Eq = ''CASE WHEN d1.[''+@Col+''] is null THEN 71428763405345098745098.8723 ELSE d1.[''+@Col+''] END=''+
''CASE WHEN d2.[''+@Col+''] is null THEN 71428763405345098745098.8723 ELSE d2.[''+@Col+''] END ''
end
else if @xType = 62 or @xType = 59 -- 62 - float, 59 - real
begin 
set @Eq = ''CASE WHEN d1.[''+@Col+''] is null THEN 8764589764.22708E237 ELSE d1.[''+@Col+''] END=''+
''CASE WHEN d2.[''+@Col+''] is null THEN 8764589764.22708E237 ELSE d2.[''+@Col+''] END ''
end
else if @xType = 52 or @xType = 48 or @xType = 104 -- smallint, tinyint, bit
begin
set @Eq = ''CASE WHEN d1.[''+@Col+''] is null THEN 99999 ELSE d1.[''+@Col+''] END=''+
''CASE WHEN d2.[''+@Col+''] is null THEN 99999 ELSE d2.[''+@Col+''] END ''
end
else if @xType = 36 -- 36 - id 
begin
set @Eq = ''CASE WHEN d1.[''+@Col+''] is null''+
'' THEN CONVERT(uniqueidentifier,''''1CD827A0-744A-4866-8401-B9902CF2D4FB'''')''+
'' ELSE d1.[''+@Col+''] END=''+
''CASE WHEN d2.[''+@Col+''] is null''+
'' THEN CONVERT(uniqueidentifier,''''1CD827A0-744A-4866-8401-B9902CF2D4FB'''')''+
'' ELSE d2.[''+@Col+''] END''
end
else if @xType = 61 or @xType = 58 -- datetime, smalldatetime
begin
set @Eq =
''CASE WHEN d1.[''+@Col+''] is null THEN ''''!#null$'''' ELSE CONVERT(varchar(40),d1.[''+@Col+''],109) END=''+
''CASE WHEN d2.[''+@Col+''] is null THEN ''''!#null$'''' ELSE CONVERT(varchar(40),d2.[''+@Col+''],109) END ''
end
else if @xType = 34
begin
set @Eq = ''ISNULL(DATALENGTH(d1.[''+@Col+'']),0)=ISNULL(DATALENGTH(d2.[''+@Col+'']),0) '' 
end
else if @xType = 35 or @xType = 99 -- text (35),ntext (99) 
begin
set @Eq = ''ISNULL(SUBSTRING(d1.[''+@Col+''],1,DATALENGTH(d1.[''+@Col+
''])),''''!#null$'''')=ISNULL(SUBSTRING(d2.[''+@Col+''],1,DATALENGTH(d2.[''+@Col+''])),''''!#null$'''') ''
end
else if @xType = 189 
begin
if '+STR(@NoTimestamp)+' = 0 
set @Eq = ''d1.[''+@Col+'']=d2.[''+@Col+''] ''
else
set @Eq = ''1=1''
end
else if @xType = 98 -- SQL_variant
begin
set @Eq = ''ISNULL(d1.[''+@Col+''],''''!#null$'''')=ISNULL(d2.[''+@Col+''],''''!#null$'''') ''
end
if @AndStr = ''''
set @AndStr = @AndStr + CHAR(10) + '' '' + @Eq 
else
if len(@AndStr) + len('' and '' + @Eq)&lt;8000
set @AndStr = @AndStr + '' and '' + CHAR(10) + '' '' + @Eq 
else
begin
set @StrInd = @StrInd + 1
Insert into ##CompareStr values(@StrInd,@AndStr)
set @AndStr = '' and '' + @Eq 
end
fetch next from Cols into @Col, @xType, @len 
end 
deallocate Cols '
set @SqlStrCompareUKeyTables = '
if @KeyAndStr &lt;&gt; ''''
begin
set @SelectStr = ''SELECT ''+ @KeyCommaStr+'' INTO ##NotInDb2 FROM '+@db1+'.[''+@d1User+''].[''+@Tab+''] d1 ''+ 
'' WHERE not exists''+CHAR(10)+'' (SELECT * FROM '+@db2+'.[''+@d2User+''].[''+@Tab+''] d2 ''+ 
'' WHERE ''+CHAR(10)+@KeyAndStr+'')''
if '+STR(@VerboseLevel)+' = 1
print CHAR(10)+''To find rows that are in '+@db1+', but are not in db2:''+CHAR(10)+
REPLACE (@SelectStr, ''into ##NotInDB2'','''')
exec (@SelectStr) 
if @@rowcount &gt; 0 
set @NotInDB2 = 1 
set @SelectStr = ''SELECT ''+@KeyCommaStr+'' INTO ##NotInDB1 FROM '+@db2+'.[''+@d2User+''].[''+@Tab+''] d1 ''+ 
'' WHERE not exists''+CHAR(10)+'' (SELECT * FROM '+@db1+'.[''+@d1User+''].[''+@Tab+''] d2 ''+ 
'' WHERE ''+CHAR(10)+@KeyAndStr+'')'' 
if '+STR(@VerboseLevel)+' = 1
print CHAR(10)+''To find rows that are in '+@db2+', but are not in '+@db1+':''+CHAR(10)+
REPLACE (@SelectStr, ''into ##NotInDB1'','''')
exec (@SelectStr) 
if @@rowcount &gt; 0 
set @NotInDB1 = 1 
-- if there are non-key columns
if @AndStr &lt;&gt; '''' 
begin
set @PrintStr = '' Print ''
set @ExecStr = '' exec (''
set @SqlStr = ''''
Insert into ##CompareStr values(1,
''SELECT ''+ @KeyCommaStr+'' INTO ##NotEq FROM '+@db2+'.[''+@d2User+''].[''+@Tab+''] d1 ''+ 
'' INNER JOIN '+@db1+'.[''+@d1User+''].[''+@Tab+''] d2 ON ''+CHAR(10)+@KeyAndStr+CHAR(10)+''WHERE not('') 
-- Adding last string in temp table containing a comparing string to execute
set @StrInd = @StrInd + 1
Insert into ##CompareStr values(@StrInd,@AndStr+'')'')
set @i = 1
while @i &lt;= @StrInd
begin
set @SqlStr = @SqlStr + '' declare @Str''+LTRIM(STR(@i))+'' varchar(8000) ''+
''select @Str''+LTRIM(STR(@i))+''=CompareStr FROM ##CompareStr WHERE ind = ''+STR(@i)
if @ExecStr &lt;&gt; '' exec (''
set @ExecStr = @ExecStr + ''+''
if @PrintStr &lt;&gt; '' Print ''
set @PrintStr = @PrintStr + ''+''
set @ExecStr = @ExecStr + ''@Str''+LTRIM(STR(@i))
set @PrintStr = @PrintStr + '' REPLACE(@Str''+LTRIM(STR(@i))+'','''' into ##NotEq'''','''''''') ''
set @i = @i + 1
end
set @ExecStr = @ExecStr + '') ''
set @ExecSqlStr = @SqlStr + @ExecStr 
set @PrintSqlStr = @SqlStr + 
'' Print CHAR(10)+''''To find rows that are different in non-key columns:'''' ''+
@PrintStr 
if '+STR(@VerboseLevel)+' = 1
exec (@PrintSqlStr)
exec (@ExecSqlStr)
  
if @@rowcount &gt; 0 
set @NotEq = 1 
end
else
if '+STR(@VerboseLevel)+' = 1
print CHAR(10)+''There are no non-key columns in the table''
truncate table ##CompareStr
if @NotInDB1 = 1 or @NotInDB2 = 1 or @NotEq = 1
begin 
print CHAR(10)+''Data are different''
if @NotInDB2 = 1 and '+STR(@NumbToShow)+' &gt; 0
begin
print ''These key values exist in '+@db1+', but do not exist in '+@db2+': ''
set @SelectStr = ''select top ''+STR('+STR(@NumbToShow)+')+'' * from ##NotInDB2''
exec (@SelectStr)
end
if @NotInDB1 = 1 and '+STR(@NumbToShow)+' &gt; 0
begin
print ''These key values exist in '+@db2+', but do not exist in '+@db1+': ''
set @SelectStr = ''select top ''+STR('+STR(@NumbToShow)+')+'' * from ##NotInDB1''
exec (@SelectStr)
end
if @NotEq = 1 and '+STR(@NumbToShow)+' &gt; 0
begin
print ''Row(s) with these key values contain differences in non-key columns: ''
set @SelectStr = ''select top ''+STR('+STR(@NumbToShow)+')+'' * from ##NotEq''
exec (@SelectStr) 
end
exec (''insert into #DiffTables values(''''[''+@Tab+'']'''')'') 
end 
else
begin
print CHAR(10)+''Data are identical''
exec ('' insert into #IdenticalTables values(''''[''+@Tab+'']'''')'') 
end
if exists (select * from tempdb.dbo.sysobjects where name like ''##NotEq%'')
drop table ##NotEq
end 
else '
set @SqlStrCompareNonUKeyTables = '
begin
exec (''insert into #NoPKTables values(''''[''+@Tab+'']'''')'')
set @PrintStr = '' Print ''
set @ExecStr = '' exec (''
set @SqlStr = ''''
Insert into ##CompareStr values(1,
''SELECT ''+
'' * INTO ##NotInDB2 FROM '+@db1+'.[''+@d1User+''].[''+@Tab+''] d1 WHERE not exists ''+CHAR(10)+
'' (SELECT * FROM '+@db2+'.[''+@d2User+''].[''+@Tab+''] d2 WHERE '')
set @StrInd = @StrInd + 1
Insert into ##CompareStr values(@StrInd,@AndStr+'')'')
set @i = 1
while @i &lt;= @StrInd
begin
set @SqlStr = @SqlStr + '' declare @Str''+LTRIM(STR(@i))+'' varchar(8000) ''+
''select @Str''+LTRIM(STR(@i))+''=CompareStr FROM ##CompareStr WHERE ind = ''+STR(@i)
if @ExecStr &lt;&gt; '' exec (''
set @ExecStr = @ExecStr + ''+''
if @PrintStr &lt;&gt; '' Print ''
set @PrintStr = @PrintStr + ''+''
set @ExecStr = @ExecStr + ''@Str''+LTRIM(STR(@i))
set @PrintStr = @PrintStr + '' REPLACE(@Str''+LTRIM(STR(@i))+'','''' into ##NotInDB2'''','''''''') ''
set @i = @i + 1
end
set @ExecStr = @ExecStr + '') ''
set @ExecSqlStr = @SqlStr + @ExecStr 
set @PrintSqlStr = @SqlStr +
'' Print CHAR(10)+''''To find rows that are in '+@db1+', but are not in '+@db2+':'''' ''+
@PrintStr 
if '+STR(@VerboseLevel)+' = 1
exec (@PrintSqlStr)
exec (@ExecSqlStr)
  
if @@rowcount &gt; 0 
set @NotInDB2 = 1 
delete from ##CompareStr where ind = 1
set @PrintStr = '' Print ''
set @ExecStr = '' exec (''
set @SqlStr = ''''
Insert into ##CompareStr values(1,
''SELECT ''+
'' * INTO ##NotInDB1 FROM '+@db2+'.[''+@d2User+''].[''+@Tab+''] d1 WHERE not exists ''+CHAR(10)+
'' (SELECT * FROM '+@db1+'.[''+@d1User+''].[''+@Tab+''] d2 WHERE '')
set @i = 1
while @i &lt;= @StrInd
begin
set @SqlStr = @SqlStr + '' declare @Str''+LTRIM(STR(@i))+'' varchar(8000) ''+
''select @Str''+LTRIM(STR(@i))+''=CompareStr FROM ##CompareStr WHERE ind = ''+STR(@i)
if @ExecStr &lt;&gt; '' exec (''
set @ExecStr = @ExecStr + ''+''
if @PrintStr &lt;&gt; '' Print ''
set @PrintStr = @PrintStr + ''+''
set @ExecStr = @ExecStr + ''@Str''+LTRIM(STR(@i))
set @PrintStr = @PrintStr + '' REPLACE(@Str''+LTRIM(STR(@i))+'','''' into ##NotInDB1'''','''''''') ''
set @i = @i + 1
end
set @ExecStr = @ExecStr + '') ''
set @ExecSqlStr = @SqlStr + @ExecStr 
set @PrintSqlStr = @SqlStr +
'' Print CHAR(10)+''''To find rows that are in '+@db2+', but are not in '+@db1+':'''' ''+
@PrintStr 
if '+STR(@VerboseLevel)+' = 1
exec (@PrintSqlStr)
exec (@ExecSqlStr)
  
if @@rowcount &gt; 0 
set @NotInDB1 = 1 
truncate table ##CompareStr
if @NotInDB1 = 1 or @NotInDB2 = 1
begin 
print CHAR(10)+''Data are different''
if @NotInDB2 = 1 and '+STR(@NumbToShow)+' &gt; 0
begin
print ''The row(s) exist in '+@db1+', but do not exist in '+@db2+': ''
set @SelectStr = ''select top ''+STR('+STR(@NumbToShow)+')+'' * from ##NotInDB2''
exec (@SelectStr)
end
if @NotInDB1 = 1 and '+STR(@NumbToShow)+' &gt; 0
begin
print ''The row(s) exist in '+@db2+', but do not exist in '+@db1+': ''
set @SelectStr = ''select top ''+STR('+STR(@NumbToShow)+')+'' * from ##NotInDB1''
exec (@SelectStr)
end
exec (''insert into #DiffTables values(''''[''+@Tab+'']'''')'') 
end 
else
begin
print CHAR(10)+''Data are identical''
exec ('' insert into #IdenticalTables values(''''[''+@Tab+'']'''')'') 
end
end
if exists (select * from tempdb.dbo.sysobjects where name like ''##NotInDB1%'')
drop table ##NotInDB1
if exists (select * from tempdb.dbo.sysobjects where name like ''##NotInDB2%'')
drop table ##NotInDB2
NextTab:
fetch next from TabCur into @Tab, @d1User, @d2User 
end 
deallocate TabCur 
'
exec (@SqlStrGetListOfKeys1+@SqlStrGetListOfKeys2+@SqlStrGetListOfColumns+
@SqlStrCompareUKeyTables+@SqlStrCompareNonUKeyTables)
print ' '
SET NOCOUNT OFF
if (select count(*) from #NoPKTables) &gt; 0
begin
select name as 'Table(s) without Unique key:' from #NoPKTables 
end
if (select count(*) from #DiffTables) &gt; 0
begin
select name as 'Table(s) with the same name & structure, but different data:' from #DiffTables 
end
else
print CHAR(10)+'No tables with the same name & structure, but different data'+CHAR(10)
if (select count(*) from #IdenticalTables) &gt; 0
begin
select name as 'Table(s) with the same name & structure and identical data:' from #IdenticalTables 
end
if (select count(*) from #EmptyTables) &gt; 0
begin
select name as 'Table(s) with the same name & structure and empty in the both databases:' from #EmptyTables 
end
drop table #TabToCheck
drop table ##CompareStr
drop table #DiffTables
drop table #IdenticalTables
drop table #EmptyTables
drop table #NoPKTables
drop table #IndList1
drop table #IndList2
return</pre>