---
title: 执行命令 -hexo g 报错
date: 2023-12-21 23:33:12
categories:
  - bug
---
# 报错信息：

````bash
PS D:\idea_project\star_world\source\_posts> hexo g
INFO  Validating config
INFO  Start processing
Prism's Diff Highlight plugin requires the Diff language definition (prism-diff.js).Make sure the language definition is loaded or use Prism's Autoloader plugin.
WARN  [SXEC 201] Essential information(title, desc, lang, etc) config incorrectly, Page will render incorrectly
X [ERROR] Could not resolve "node_modules/hexo-theme-shokax/source/js/_app/pjax/siteInit.js"

FATAL Something's wrong. Maybe you can find the solution here: https://hexo.io/docs/troubleshooting.html
Error: Build failed with 1 error:
error: Could not resolve "node_modules/hexo-theme-shokax/source/js/_app/pjax/siteInit.js"
    at failureErrorWithLog (D:\idea_project\star_world\node_modules\esbuild\lib\main.js:1650:15)
    at D:\idea_project\star_world\node_modules\esbuild\lib\main.js:1058:25
    at D:\idea_project\star_world\node_modules\esbuild\lib\main.js:1003:52
    at buildResponseToResult (D:\idea_project\star_world\node_modules\esbuild\lib\main.js:1056:7)
    at D:\idea_project\star_world\node_modules\esbuild\lib\main.js:1085:16
    at responseCallbacks.<computed> (D:\idea_project\star_world\node_modules\esbuild\lib\main.js:703:9)
    at handleIncomingPacket (D:\idea_project\star_world\node_modules\esbuild\lib\main.js:763:9)
    at Socket.readFromStdout (D:\idea_project\star_world\node_modules\esbuild\lib\main.js:679:7)
    at Socket.emit (node:events:514:28)
    at addChunk (node:internal/streams/readable:545:12)

````

# 解决方案

````bash
PS D:\idea_project\star_world> hexo g
````

- 切换到**shokaX根目录**即可解决此问题

# 报错信息

```bash
PS D:\project\star_world> hexo new 创业目录
hexo : 无法将“hexo”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径正确，然后再试 一次。
所在位置 行:1 字符: 1
+ hexo new 创业目录
+ ~~~~
    + CategoryInfo          : ObjectNotFound: (hexo:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```

# 解决方案

```bash
PS D:\project\star_world> npm install -g hexo-cli
```

# 报错信息

```bash
hexo : 无法加载文件 C:\Users\zengyu\AppData\Roaming\npm\hexo.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.m
icrosoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
所在位置 行:1 字符: 1
+ hexo new 创业目录
+ ~~~~
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```

# 解决方案

```bash
set-ExecutionPolicy RemoteSigned 
```
