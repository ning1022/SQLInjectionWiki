## SQL注入检测 {docsify-ignore-all}
可以通过多种方式检测注入。其中最简单的方法是在各种参数后添加`'`或`"`从而得到一个从Web服务器返回的数据库报错信息。以下部分描述了在哪里可以找到这些参数以及如何检测这些参数。
### 参数位置
浏览下面的标签，查看各种HTTP请求中的常见注入点。常见注入点<font color="red">以红色突出显示</font>





::: tabs language

- GET - HTTP Request
    在常见的HTTP GET请求（以及大多数请求类型）中，有一些常见的注入点。例如：网址参数（下面的请求的`id`），Cookie，host以及任何自定义headers信息。然而，HTTP请求中的任何内容都可能容易受到SQL注入的攻击。
    
  ```code
  GET /?id={red}homePage{/red} HTTP/1.1
  Host: {red}www.netspi.com{/red}
  Connection: close
  Cache-Control: max-age=0
  User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36
  Upgrade-Insecure-Requests: 1
  Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
  Accept-Encoding: gzip, deflate
  Accept-Language: en-US,en;q=0.9
  X-Server-Name: {red}PROD{/red}
  Cookie: user={red}harold{/red};
  ```

- POST - Form Data
    在具有Content-Type为application/x-www-form-urlencoded的标准HTTP POST请求中，注入将类似于GET请求中的URL参数。它们位于HTTP头信息下方，但仍可以用相同的方式进行利用。

  ```code
  POST / HTTP/1.1
  Host: netspi.com.com
  Content-Type: application/x-www-form-urlencoded
  Content-Length: 39
  username={red}harold{/red}&email={red}harold@netspi.com{/red}
  ```

- POST - JSON
    在具有Content-Type为application/json的标准HTTP POST请求中，注入通常是`JSON{"key":"value"}`对的值。该值也可以是数组或对象。虽然符号是不同的，但值可以像所有其他参数一样注入。（提示：尝试使用`'`，但要确保JSON使用双引号，否则可能会破坏请求格式。）

  ```code
  POST / HTTP/1.1
  Host: netspi.com.com
  Content-Type: application/json
  Content-Length: 56
  {
  "username":"{red}harold{/red}",
  "email":"{red}harold@netspi.com{/red}"
  }
  ```
    
- POST - XML
    在具有Content-Type为application/xml的标准HTTP POST请求中，注入通常在一个内部<xmlObject></xmlObject>。虽然符号是不同的，但值可以像所有其他参数一样注入。（提示：尝试使用`'`）
    
  ```code
  POST / HTTP/1.1
  Host: netspi.com.com
  Content-Type: application/xml
  Content-Length: 79
  <root>
  <username>{red}harold{/red}</username>
  <email>{red}harold@netspi.com{/red}</email>
  </root>
  ```
    
:::

### 检测注入
通过在应用程序中触发错误和布尔逻辑，可以最轻松地检测易受攻击的参数。提供格式错误的查询将触发错误，并且使用各种布尔逻辑语句发送有效查询将触发来自Web服务器的不同响应。

注：True或False语句应通过HTTP状态码或HTML内容返回不同的响应。如果这些响应与查询的True/False性质一致，则表示存在注入。

| 描述   |      语句     |  
|----------|:-------------:|
| 逻辑测试 |  page.asp?id=1 or 1=1 -- true <br>page.asp?id=1' or 1=1 -- true<br>page.asp?id=1" or 1=1 -- true<br>page.asp?id=1 and 1=2 -- false |
| 算术 |    product.asp?id=1/1 -- true<br> product.asp?id=1/0 -- false  |
| 基于盲注：<br />检测盲注可能需要识别或猜测DBMS，<br />并检查以找到适当的时间函数。 | [看这里](/injectionTypes/blindBased)|
| 基于错误：<br>注意：使用无效语法的逻辑测试和算术<br />也可能会导致错误。 | [看这里](/injectionTypes/errorBased)|








