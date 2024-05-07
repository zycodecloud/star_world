---
title: 初识Anaconda
date: 2024-05-07 21:09:42
categories:  
  - python
---
## 1.安装

[Anaconda官网](https://www.anaconda.com/download/success) 下载对应平台的安装包

## 2.打开工具

- 在开始菜单中选择`Anaconda prompt`
- 输入代码，打开控制台

```bash
jupyter notebook
```

## 3.浏览器自动化驱动

### Selenium

- 安装Selenium

#### 系统安装

```bash
pip install selenium
```

#### notebook 安装

```bash
!pip install selenium
```

## 4.安装chromedriver

1.  [chromedriver安装](https://chromedriver.storage.googleapis.com/index.html)
2.  下载对应谷歌浏览器版本 32/64位
3.  复制到notebook目录 /  添加到系统变量中

## 5.测试环境

```python
# 导入模块
from selenium import webdriver
# 初始化
driver = webdriver.Chrome()
# 打开网址get()
driver.get("http://www.baidu.com")
# 关闭
driver.close()
```

## 6.Selenium常用API

### 返回

```python
driver.back()
```

### 前进

```python
driver.forward()
```

### 与页面进行交互

```python
# 以百度为例
# 获取输入框元素  id=id选择器 name=id的定义名
element = driver.find_element("id","name")

# 输入内容shend_keys("XXX")
element.send_keys("爬虫")

#找到搜索按钮
element2 = driver.find_element('id','su')

#点击搜索按钮
element2.click()
```

- 获取页面中的内容

```python
resource = driver.page_srouce
```

## 7.提取cookie到txt

```python
# 获取所有cookie  getcookie("xxx")=获取单个cookie
cookies = driver.get_cookies()

#导入json包
import json

# cookie转json
cookies_str = json.dumps(cookies)

#写入文件txt
with open("cookie.txt"，“w” , encoding="utf-8")

#写入
file.write(cookies_str)
```

## 8.读取存储在文件中的cookie并登录

```python
# 导入函数
from selenium import webdriver

# 定义url
url="https://www.baidu.com"

# 驱动打开浏览器
driver = webdriver.Chrome()

# 打开网址
driver.get(url)

# 打开文件
with open("cookies.txt","r",encoding="utf-8")
# 读取文件
	cookies_str = fle.read()

# 导入json包
import json

# 序列化cookie
cookies= = json.loads(cookies_str)

# 插入网页
for cookie in cookies:
# 可能会出现插入出错
	try:
		driver,add_cookie(cookie)
	except Exception as e:
		print(e)
		
# 重新访问，登录成功		
driver.get(url)

#关闭浏览器
driver.close()
```

### 命令补充

```python
# 帮助命令
help(xxx)
add?
add??

#代码提示
shift+tab
```

## 9.魔法命令

```python
# 执行外部python文件
%run xxx.py
```

```python
# 显示运行时长
%time
# 显示精度更明确
%timeit add()
```

```python
# 显示当前环境的变量
%who
# 简单显示
%who_ls
# 安装依赖
!pip install xxx
```

## 10.初识numpy

### 1.安装

```python
# Anaconda自带numpy
# 导入数据分析“三剑客”
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 查看版本
np.__version__
```

### 2.使用

```python
# 读取图片的数据
# 返回三位数组  代表RGB红绿蓝
cat = plt.imread('cat.jpg')

# 显示图片
plt.imshow(cat)

```

