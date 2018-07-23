### 数据定位 {docsify-ignore-all}

能够正确地识别和定位敏感信息可以以指数的方式减少在数据库中花费的时间，这意味着可以花费更多的时间在其他方向上。

#### 数据定位查询

##### MySQL



| 描述   |      语句     |  
|----------|:-------------:|
| 数据库大小  |    `  SELECT table_schema "Database Name",sum( data_length + index_length ) / 1024 / 1024 "Database Size in MB",sum( data_free )/ 1024 / 1024 "Free Space in MB" FROM information_schema.TABLES GROUP BY table_schema ;` |  
| 数据库名称关键字  |      `SELECT table_schema "Database Name" FROM information_schema.TABLES WHERE table_schema LIKE "%passwords%" GROUP BY table_schema ; `    |  
| 表名关键字   |      `SELECT table_schema, table_name FROM information_schema.tables WHERE table_schema NOT LIKE "information_schema" AND table_name LIKE "%admin%";`  |  
| 列名关键字  |     `SELECT column_name, table_name FROM information_schema.columns WHERE column_name LIKE "%password%";`|  
| 列数据正则表达式  |      	`SELECT * from credit_cards WHERE cc_number REGEXP '^4[0-9]{15}$';`    |  





##### Oracle

| 描述   |      语句     |  
|----------|:-------------:|
| 寻找敏感数据 |      SELECT owner,table\_name,column\_name FROM all\_tab\_columns WHERE column\_name LIKE '%PASS%';     |  
| 寻找特权  |      SELECT \* FROM session\_privs<br /> SELECT \* FROM USER\_SYS\_PRIVS <br /> SELECT \* FROM USER\_TAB\_PRIVS<br />SELECT \* FROM USER\_TAB\_PRIVS_MADE<br />SELECT \* FROM USER\_TAB\_PRIVS\_RECD<br />SELECT \* FROM ALL\_TAB\_PRIVS<br />SELECT \* FROM USER\_ROLE\_PRIVS     |  
| 提取存储过程/ Java源  |      SELECT \* FROM all\_source WHERE owner NOT IN ('SYS','SYSTEM') <br />SELECT \* FROM all\_source WHERE TYPE LIKE '%JAVA %' <br />SELECT TO\_CHAR(DBMS\_METADATA.get_ddl('TABLE','DEPT','CONSUELA')) FROM dual     |  






##### SQL Server

| 描述   |      语句     |  
|----------|:-------------:|
| 列出非默认数据库   |      SELECT NAME FROM sysdatabases WHERE (NAME NOT LIKE 'distribution') AND (NAME NOT LIKE 'master') AND (NAME NOT LIKE 'model') AND (NAME NOT LIKE 'msdb') AND (NAME NOT LIKE 'publication') AND (NAME NOT LIKE 'reportserver') AND (NAME NOT LIKE 'reportservertempdb') AND (NAME NOT LIKE 'resource') AND (NAME NOT LIKE 'tempdb') ORDER BY NAME;     |  
| 列出非默认表	   |      SELECT '[' + SCHEMA\_NAME(t.schema\_id) + '].[' + t.name + ']' AS fulltable\_name, SCHEMA\_NAME(t.schema\_id) AS schema\_name, t.name AS table\_name, i.rows FROM sys.tables AS t INNER JOIN sys.sysindexes AS i ON t.object\_id = i.id AND i.indid < 2 WHERE (ROWS> 0) AND (t.name NOT LIKE 'syscolumns') AND (t.name NOT LIKE 'syscomments') AND (t.name NOT LIKE 'sysconstraints') AND (t.name NOT LIKE 'sysdepends') AND (t.name NOT LIKE 'sysfilegroups') AND (t.name NOT LIKE 'sysfiles') AND (t.name NOT LIKE 'sysforeignkeys') AND (t.name NOT LIKE 'sysfulltextcatalogs') AND (t.name NOT LIKE 'sysindexes') AND (t.name NOT LIKE 'sysindexkeys') AND (t.name NOT LIKE 'sysmembers') AND (t.name NOT LIKE 'sysobjects') AND (t.name NOT LIKE 'syspermissions') AND (t.name NOT LIKE 'sysprotects') AND (t.name NOT LIKE 'sysreferences') AND (t.name NOT LIKE 'systypes') AND (t.name NOT LIKE 'sysusers') ORDER BY TABLE_NAME;|  
| 列名搜索  |      SELECT \* FROM INFORMATION\_SCHEMA.COLUMNS WHERE COLUMN\_NAME like '%password%'     |  
| 列出非默认列  |      SELECT * FROM INFORMATION\_SCHEMA.COLUMNS WHERE CHARACTER\_MAXIMUM\_LENGTH > 14 AND DATA\_TYPE NOT IN ('bigint','binary','bit','cursor','date','datetime','datetime2', 'datetimeoffset','float','geography','hierarchyid','image','int','money','real', 'smalldatetime','smallint','smallmoney','sql\_variant','table','time','timestamp', 'tinyint','uniqueidentifier','varbinary','xml') AND TABLE\_NAME='CreditCard' OR CHARACTER\_MAXIMUM\_LENGTH < 1 AND DATA\_TYPE NOT IN ( 'bigint', 'binary', 'bit', 'cursor', 'date', 'datetime', 'datetime2', 'datetimeoffset', 'float', 'geography', 'hierarchyid', 'image', 'int', 'money', 'real', 'smalldatetime', 'smallint', 'smallmoney', 'sql\_variant', 'table', 'time', 'timestamp', 'tinyint', 'uniqueidentifier', 'varbinary', 'xml') AND TABLE\_NAME='CreditCard' ORDER BY COLUMN_NAME;     |  
| 搜索透明加密   |      SELECT a.database\_id as [dbid], a.name, HAS\_DBACCESS(a.name) as [has\_dbaccess], SUSER\_SNAME(a.owner\_sid) as [db\_owner], a.is\_trustworthy\_on, a.is\_db\_chaining\_on, a.is\_broker\_enabled, a.is\_encrypted, a.is\_read\_only, a.create\_date, a.recovery\_model\_desc, b.filename FROM [sys].[databases] a INNER JOIN [sys].[sysdatabases] b ON a.database\_id = b.dbid ORDER BY a.database\_id WHERE is\_encrypted=1     | 
| 按数据库大小搜索   |      SELECT a.database\_id as [dbid], a.name, HAS\_DBACCESS(a.name) as [has\_dbaccess], SUSER\_SNAME(a.owner\_sid) as [db\_owner], a.is\_trustworthy\_on, a.is\_db\_chaining\_on, a.is\_broker\_enabled, a.is\_encrypted, a.is\_read\_only, a.create\_date, a.recovery\_model\_desc, b.filename, (SELECT CAST(SUM(size) * 8. / 1024 AS DECIMAL(8,2)) from sys.master\_files where name like a.name) as [DbSizeMb] FROM [sys].[databases] a INNER JOIN [sys].[sysdatabases] b ON a.database\_id = b.dbid ORDER BY DbSizeMb DESC     |  








####  数据定位正则表达式 

| 描述   |      语句     |  
|----------|:-------------:|
| 所有主要的信用卡提供商  |      ^(?:4[0-9]{12}(?:[0-9]{3})?&#124;(?:5[1-5][0-9]{2}&#124;222[1-9]&#124;22[3-9][0-9]&#124;2[3-6][0-9]{2}&#124;27[01][0-9]&#124;2720)[0-9]{12}&#124;3[47][0-9]{13}&#124;3(?:0[0-5]&#124;[68][0-9])[0-9]{11}&#124;6(?:011&#124;5[0-9]{2})[0-9]{12}&#124;(?:2131&#124;1800&#124;35\d{3})\d{11})$     |  
| Unmasked &#124; Masked SSN   |      ^(\d{3}-?\d{2}-?\d{4}&#124;XXX-XX-XXXX)$    | 


####  数据定位关键字

|  关键字  |
|----------|
|  credit  |
|  card  |
|  pin  |
|  cvv  |
|  pan  |
|  password  |
|  social  |
|  ssn  |
|  account  |
|  confidential  |














