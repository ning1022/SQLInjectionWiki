### 信息收集 {docsify-ignore-all}


收集有关任何测试环境的信息通常很有价值; 版本号，用户帐户和数据库都有助于升级漏洞。以下是常见的方法。

`*`需要特权用户

#### MySQL
| 描述   |      语句     |  
|----------|:-------------:|
| 版本   |      SELECT @@version     |  
| 单个用户   |      SELECT user() <br />SELECT system_user()     |  
| 所有用户   |      SELECT user FROM mysql.user <br /> `* `SELECT Super_priv FROM mysql.user WHERE user= 'root' LIMIT 1,1     |  
|  表   |      SELECT table\_schema, table\_name FROM information_schema.tables     |  
| 列  |      SELECT table\_name, column\_name FROM information\_schema.columns    |  
| 数据库   |      SELECT schema\_name FROM information\_schema.schemata     |  
| 当前数据库名称   |      SELECT database()     |  
| 查询其他数据库  |      USE [database_name]; SELECT database();  <br />SELECT [column] FROM [database\_name].[table\_name]     |  
| 列数   |      SELECT count(*) FROM information\_schema.columns WHERE table\_name = '[table_name]'     |  
| DBA账户   |      	SELECT host, user FROM mysql.user WHERE Super_priv = 'Y'   |  
| 密码哈希   |     SELECT host, user, password FROM mysql.user     |  
| Schema   |      SELECT schema()     |  
| 数据路径   |      SELECT @@datadir     |  
| 读取文件   |      `*` SELECT LOAD_FILE('/etc/passwd')     |  



#### Oracle

| 描述   |      语句     |  
|----------|:-------------:|
| 版本   |      	SELECT banner FROM v$version WHERE banner LIKE 'Oracle%';<br />SELECT banner FROM v$version WHERE banner LIKE 'TNS%';<br />SELECT version FROM v$instance;     |  
| 单个用户   |      SELECT user FROM dual     |  
| 所有用户   |      SELECT username FROM all_users ORDER BY username;<br />`* `SELECT name FROM sys.user$;     |  
|   表 |      SELECT table\_name FROM all\_tables; <br />SELECT owner, table\_name FROM all\_tables;     |  
| 通过列名称获取表   |      SELECT owner, table\_name FROM all\_tab\_columns WHERE column\_name LIKE '%PASS%';     |  
| 列   |      SELECT column\_name FROM all\_tab\_columns WHERE table\_name = 'blah';  <br />SELECT column\_name FROM all\_tab\_columns WHERE table_name = 'blah' and owner = 'foo';     |  
| 当前数据库名称  |      	SELECT global\_name FROM global\_name;<br />SELECT name FROM V$DATABASE; <br />SELECT instance\_name FROM V$INSTANCE;<br />SELECT SYS.DATABASE_NAME FROM DUAL;     |  
| 数据库   |      SELECT DISTINCT owner FROM all_tables;    |  
| DBA 账户   |      SELECT DISTINCT grantee FROM dba\_sys\_privs WHERE ADMIN_OPTION = 'YES';     |  
| 特权   |      SELECT \* FROM session_privs;(Retrieves Current Privs)<br /> `*` SELECT \* FROM dba\_sys\_privs WHERE grantee = 'DBSNMP';<br /> `*` SELECT grantee FROM dba\_sys\_privs WHERE privilege = 'SELECT ANY DICTIONARY';<br />SELECT GRANTEE, GRANTED\_ROLE FROM DBA\_ROLE\_PRIVS;     |  
| DB文件的位置   |      	SELECT name FROM V$DATAFILE;     |  
| 主机名，IP地址  |      SELECT UTL\_INADDR.get\_host\_name FROM dual;<br />SELECT host\_name FROM v$instance;<br />SELECT UTL\_INADDR.get\_host\_address FROM dual; (IP地址)<br />SELECT UTL\_INADDR.get\_host\_name('10.0.0.1') FROM dual; (主机名)     |  



#### SQL Server

| 描述   |      语句     |  
|----------|:-------------:|
| 版本   |      SELECT @@version;     |  
| 单个用户   |      SELECT user;<br />SELECT system\_user;<br />SELECT user_name();<br />SELECT loginame from master..sysprocesses where spid = @@SPID|  
| 所有用户   |      SELECT name from master..syslogins     |  
| 表   |      SELECT table\_catalog, table\_name FROM information_schema.columns     |  
| 列   |      SELECT table\_catalog, column\_name FROM information_schema.columns     |  
| 所有数据库   |      SELECT name from master..sysdatabases;     |  
| 当前数据库  |      SELECT db_name();     |  
| 服务器名称   |      SELECT @@SERVERNAME     |  
| 查找存储过程   |      SELECT * from master..sysobjects where name like 'sp%' order by name desc     |  
| 通过用户名获取SUID   |      SELECT SUSER_ID('sa')     |  
| 通过SUID获取用户名  |      SELECT SUSER_NAME(1)     |  
| 检查账户是不是管理员  |      IS\_SRVROLEMEMBER(convert(varchar,0x73797361646D696E)) <br />SELECT is_srvrolemember('sysadmin');     |  
| Policies   |      ` SELECT p.policy_id, p.name as [PolicyName], p.condition_id, c.name as [ConditionName], c.facet, c.expression as [ConditionExpression], p.root_condition_id, p.is_enabled, p.date_created, p.date_modified, p.description, p.created_by, p.is_system, t.target_set_id, t.TYPE, t.type_skeleton FROM msdb.dbo.syspolicy_policies p INNER JOIN syspolicy_conditions c ON p.condition_id = c.condition_id INNER JOIN msdb.dbo.syspolicy_target_sets t ON t.object_set_id = p.object_set_id `    |  
| 域用户   |      [https://raw.githubusercontent.com/NetSPI/PowerUpSQL/master/templates/tsql/Get-SQLDomainUser-Example.sql](https://raw.githubusercontent.com/NetSPI/PowerUpSQL/master/templates/tsql/Get-SQLDomainUser-Example.sql)     |  
| DB 审计  |      ` SELECT a.audit_id, a.name as audit_name, s.name as database_specification_name, d.audit_action_name, d.major_id, OBJECT_NAME(d.major_id) as object, s.is_state_enabled, d.is_group, s.create_date, s.modify_date, d.audited_result FROM sys.server_audits AS a JOIN sys.database_audit_specifications AS s ON a.audit_guid = s.audit_guid JOIN sys.database_audit_specification_details AS d ON s.database_specification_id = d.database_specification_id  `    | 
| Server 审计   |   ` SELECT audit_id, a.name as audit_name, s.name as server_specification_name, d.audit_action_name, s.is_state_enabled, d.is_group, d.audit_action_id, s.create_date, s.modify_date FROM sys.server_audits AS a JOIN sys.server_audit_specifications AS s ON a.audit_guid = s.audit_guid JOIN sys.server_audit_specification_details AS d ON s.server_specification_id = d.server_specification_id   `  |  
| 查询历史记录  |     ` SELECT * FROM (SELECT COALESCE(OBJECT_NAME(qt.objectid),'Ad-Hoc') AS objectname, qt.objectid as objectid, last_execution_time, execution_count, encrypted,(SELECT TOP 1 SUBSTRING(qt.TEXT,statement_start_offset / 2+1,( (CASE WHEN statement_end_offset = -1 THEN (LEN(CONVERT(NVARCHAR(MAX),qt.TEXT)) * 2) ELSE statement_end_offset END)- statement_start_offset) / 2+1)) AS sql_statement FROM sys.dm_exec_query_stats AS qs CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS qt ) x ORDER BY execution_count DESC `    |  











