---
title: 使用native-image编译springboot项目为exe可执行文件
date: 2024-04-17 16:56:10
categories:
  - JAVA编程
---
### 安装 GraalVM JDK

1. 下载并安装 [GraalVM JDK](https://github.com/graalvm/graalvm-ce-builds/releases/tag/vm-22.3.1)

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.7zq8reh29o.png)

1. 设置环境变量 `JAVA_HOME` 为 GraalVM JDK 的安装路径
2. 添加 GraalVM JDK 的 bin 目录到 PATH 环境变量
3. 运行 `java -version` 命令，检查是否成功安装和配置 GraalVM JDK

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.99t5xq1l4t.webp)

### 安装 native-image

1. 打开cmd。输入`gu install native-image` (需要科学上网，如果下载不了，建议下载GraalVM JDK 下的jar包进行导入)
2. 输入`gu list`查看是否安装成功

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.54xklm74nl.webp)

### 安装 Visual studio

1. 下载并安装 [Visual Studio](https://visualstudio.microsoft.com/zh-hans/)
2. 选择Visual Studio Community 2022![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.5q787x2ly5.webp)

1. 下载完成后打开安装文件

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.64dnyscd9l.webp)

1. 工作负载，选择使用C++桌面开发

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.9dcrvg0s4x.webp)

1. 单个组件，搜索msvc，选择2-8，搜索win，选择win10--11 SDK

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.2yy5zuk7lp.webp)

1. 语言包，选择英文

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.6wqjgiwbpn.webp)

1. 点击安装

### 打包springboot为.exe文件
1. 新建spring boot项目，导入`Graalvm Native`

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.32hrxkfxyy.webp)

1. 勾选配置文件中的`native`，执行`clean`，`native:compile`

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.361dvab18l.webp)

### 成功导出exe文件

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.7ljt0kcfg7.webp)

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.3uunfbo6jg.webp)

启动速度也是非常之快

![](https://github.com/zycodecloud/picx-images-hosting/raw/master/image.51dynxdtj9.webp)



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
