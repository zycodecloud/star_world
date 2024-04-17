---
title: 使用native-image编译springboot项目为exe可执行文件
date: 2024-04-17 16:56:10
tags:
  - 编程
---
### 安装 GraalVM JDK

1. 下载并安装 [GraalVM JDK](https://github.com/graalvm/graalvm-ce-builds/releases/tag/vm-22.3.1)
2. 设置环境变量 `JAVA_HOME` 为 GraalVM JDK 的安装路径
3. 添加 GraalVM JDK 的 bin 目录到 PATH 环境变量
4. 运行 `java -version` 命令，检查是否成功安装和配置 GraalVM JDK

### 安装 native-image

1. 打开cmd。输入`gu install native-image`
2. 输入`gu list`查看是否安装成功

### 安装Visual studio

1. 下载并安装 [Visual Studio](https://visualstudio.microsoft.com/zh-hans/)
2. 选择Visual Studio Community 2022
3. 下载完成后打开安装文件
4. 工作负载，选择使用C++桌面开发，C++移动开发
5. 单个组件，搜索msvc，选择2-8，搜索win，选择win11 SDK
6. 语言包，选择英文
7. 点击安装，关闭

### 打包springboot为jar文件
1. 打开cmd，输入`mvn package`
2. 打包完成后，在target目录下找到打包好的jar文件
3. 打开x64 Native Tools Command prompt for VS 2022
4. 进入jar文件所在目录
5. 使用`native-image -jar your-jar-file.jar`命令将jar文件转换为exe文件 
