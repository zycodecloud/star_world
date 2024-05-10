---
title: 使用native-image编译springboot项目为exe可执行文件
date: 2024-04-17 16:56:10
categories:
  - JAVA编程
---
### 安装 GraalVM JDK

1. 下载并安装 [GraalVM JDK](https://github.com/graalvm/graalvm-ce-builds/releases/tag/vm-22.3.1)

![](https://t.tutu.to/img/ovfg)

1. 设置环境变量 `JAVA_HOME` 为 GraalVM JDK 的安装路径
2. 添加 GraalVM JDK 的 bin 目录到 PATH 环境变量
3. 运行 `java -version` 命令，检查是否成功安装和配置 GraalVM JDK

![](https://t.tutu.to/img/V1gd)

### 安装 native-image

1. 打开cmd。输入`gu install native-image` (需要科学上网，如果下载不了，建议下载GraalVM JDK 下的jar包进行导入)
2. 输入`gu list`查看是否安装成功

![](https://t.tutu.to/img/VgLn)

### 安装 Visual studio

1. 下载并安装 [Visual Studio](https://visualstudio.microsoft.com/zh-hans/)
2. 选择Visual Studio Community 2022![](https://t.tutu.to/img/Vw9R)

1. 下载完成后打开安装文件

![](https://t.tutu.to/img/VTqG)

1. 工作负载，选择使用C++桌面开发

![](https://t.tutu.to/img/o8Ym)

1. 单个组件，搜索msvc，选择2-8，搜索win，选择win10--11 SDK

![](https://t.tutu.to/img/VQwo)

1. 语言包，选择英文

![](https://t.tutu.to/img/V4QJ)

1. 点击安装

### 打包springboot为.exe文件
1. 新建spring boot项目，导入`Graalvm Native`

![](https://t.tutu.to/img/oUNX)

1. 勾选配置文件中的`native`，执行`clean`，`native:compile`

![](https://t.tutu.to/img/VymH)

### 成功导出exe文件

![](https://t.tutu.to/img/Vjge)

![](https://t.tutu.to/img/VtOi)

启动速度也是非常之快

![](https://t.tutu.to/img/o0Qj)



### 报错情况

#### 1.返回值不为零

```bash
Execution of D:\project_resource\graalvm-ce-java17-22.3.0\bin\native-image.cmd @target\tmp\native-image-2476901968031795160.args com.chx.hengxinmain.HengxinMainApplication returned non-zero result
```

>  解决方案

1. 在开始菜单，进入`x64 Native Tools Command Prompt for VS 2022`
2. 输入`native-image @target\tmp\native-image-2476901968031795160.args `

#### 2.找不到启动类

```bash
Error: Please specify class (or <module>/<mainclass>) containing the main entry point method. (see --help)
```

> 解决方案

1. 在pom.xml文件中配置

```xml
<plugin>
    <groupId>org.graalvm.buildtools</groupId>
    <artifactId>native-maven-plugin</artifactId>
    <configuration>
    	<mainClass>com.chx.hengxinmain.HengxinMainApplication</mainClass>
    </configuration>
</plugin>
```

2. 在开始菜单，进入`x64 Native Tools Command Prompt for VS 2022`

3. 输入`native-image @target\tmp\native-image-2476901968031795160.args com.chx.hengxinmain.HengxinMainApplication `
