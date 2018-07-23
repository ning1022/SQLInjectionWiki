### 横向移动 {docsify-ignore-all}
横向移动允许测试人员访问不同的功能/数据集，这些功能/数据不明确要求为特权的用户。横向切换用户帐户将暴露不同的信息，并可能有助于损害更多特权用户。



#### MySQL


`*`需要特权用户

| 描述   |      语句     |  
|----------|:-------------:|
| 创建用户   |      CREATE USER 'netspi'@'%' IDENTIFIED BY 'password'     |  
| 删除用户   |      DROP USER netspi     |  


#### Oracle


| 描述   |      语句     |  
|----------|:-------------:|
| 创建用户   |      CREATE USER user IDENTIFIED by pass;     |  
|删除用户  |      DROP USER user     |  


#### SQL Server

`*`需要特权用户

| 描述   |      语句     |  
|----------|:-------------:|
| 创建用户   |     EXEC sp_addlogin 'user', 'pass';    |  
| 删除用户  |      EXEC sp_droplogin 'user';     |  
| 链接抓取   |      [https://blog.netspi.com/sql-server-link-crawling-powerupsql/](https://blog.netspi.com/sql-server-link-crawling-powerupsql/)     |  
| 作为当前服务连接到远程数据库   |      --Requires sysadmin <br />SELECT * FROM OPENDATASOURCE('SQLNCLI', 'Server=MSSQLSRV04\SQLSERVER2016;Trusted_Connection=yes;').master.dbo.sysdatabases     |  





