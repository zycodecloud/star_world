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
