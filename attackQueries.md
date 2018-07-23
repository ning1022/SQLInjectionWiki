### 攻击查询 {docsify-ignore}
一旦识别出可注射参数和DBMS类型，我们就需要攻击数据库。您可以在下面找到各种部分，以帮助您升级权限，扩展数据等。

#### [信息收集](/attackQueries/informationGathering)
收集有关任何测试环境的信息通常很有价值; 版本号，用户帐户和数据库都有助于升级漏洞。以下是常见的方法。

#### [数据定位](/attackQueries/dataTargeting)
能够正确地识别和定位敏感信息可以以指数的方式减少在数据库中花费的时间，这意味着可以花费更多的时间在其他方向上。

#### [提升特权](/attackQueries/privilegeEscalation)
某些功能需要特权用户，并且为了升级漏洞，特权用户始终是第一步。

####  [执行系统命令](/attackQueries/executingOSCommands)
执行系统命令是SQL注入的主要目标之一，这有助于完全控制主机操作系统。这可能通过直接执行命令，修改现有数据以在网页上放置shell或者利用数据库中的隐藏功能来实现。

####  [读写文件](/attackQueries/readingAndWritingFiles)
读取和写入文件有助于数据收集和数据泄露。许多方法包括写入webroot，这可以执行webshell，或允许数据通过端口80/443被泄露。

####  [横向移动](/attackQueries/lateralMovement)
横向移动允许测试人员访问不同的功能/数据集，这些功能/数据不明确要求为特权的用户。横向切换用户帐户将暴露不同的信息，并可能有助于损害更多特权用户。



####  [数据泄漏](/attackQueries/dataExfiltration)
泄漏的数据以及任何受损数据的脱机副本可以用来做数据分析。数据可以通过文件，各种Layer 4请求和隐藏技术被泄漏。


