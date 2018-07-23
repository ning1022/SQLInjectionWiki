### 条件语句 {docsify-ignore-all}

条件语句有助于创建复杂的查询并帮助盲注入。

#### MySQL

| 描述   |      语句     |  
|----------|:-------------:|
| If/Else   |      SELECT IF(1=2,'true','false')    |  
| 逻辑 OR   |      SELECT 1 &#124;&#124; 0<br  /> 看到这个运算符的细微差别去[这里](https://dev.mysql.com/doc/refman/5.7/en/logical-operators.html#operator_or)    |  
 





#### Oracle

| 描述   |      语句     |  
|----------|:-------------:|
|Case     |     SELECT CASE WHEN 1=1 THEN 1 ELSE 2 END FROM dual |




#### SQL Server

| 描述   |      语句     |  
|----------|:-------------:|
| Case   |      SELECT CASE WHEN 1=1 THEN 1 ELSE 0 END     |  
| If/Else   |     IF 1=2 SELECT 'true' ELSE SELECT 'false';     |  

