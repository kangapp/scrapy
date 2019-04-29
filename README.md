# scrapy

## 开发环境

- pycharm编辑器

- mysql+navicat

- python2和python3

- 虚拟环境的安装和配置

  > pip install virtualenv  
  [使用python豆瓣源](https://www.douban.com/note/302711300/)  
  virtualenv [name] //在当前目录下创建python虚拟环境  
  activate  //进入虚拟环境，在Scripts目录下  
  deactivate  //退出虚拟环境  
  virtualenv -p [python.exe] [name] //创建指定版本的python虚拟环境  
  pip install virtualenvwrapper-win  安装虚拟环境管理  
  WORKON_HOME=[工作目录]   //设置环境变量  
  mkvirtualenv [name]  //创建虚拟环境并自动进入  
  mkvirtualenv --python [python.exe] [name]  //创建指定版本的虚拟环境并自动进入  
  workon  //查看虚拟环境  
  workon [name]  //进入虚拟环境  

## 前置知识

### 正则表达式

```
# -*- coding: utf-8 -*-
__author__ = 'kang'

import re

# line = "xxx出生于2001年6月"
# line = "xxx出生于2001/6/1"
# line = "xxx出生于2001-6-1"
# line = "xxx出生于2001-06-01"
# line = "xxx出生于2001-06"
line = "xxx出生于2001年6月1日"

regex_str = ".*出生于(\d{4}[年/-]\d{1,2}([月/-]|$)(\d{1,2}(日|$)|$))"

match_obj = re.match(regex_str, line)
if match_obj:
    print(match_obj.group(1))
```

### 爬虫去重策略

- 将访问过的url保存到数据库中

- 将访问过的url保存到set中，只需要o(1)的代价就可以查询，占用过多内存

- url经过md5等方法哈希后保存到set中

- 用bitmap方法，将访问过的url通过hash函数映射到某一位

- bloomfilter方法对bitmap进行改进，多重hash函数降低冲突

### 字符串编码

### 深度优先和广度优先

## 爬虫示例

### 新建项目

> mkvirtualenv article_spider //创建并进入python虚拟环境  
pip install -i https://pypi.douban.com/simple/ scrapy  //安装scrapy  
scrapy startproject ArticleSpider //在虚拟机环境目录创建项目  
scrapy genspider jobbole blog.jobbole.com  //进入项目目录生成爬虫模板  
pip install -i https://pypi.douban.com/simple/ pypiwin32 //widows需要安装才能启动  
scrapy crawl jobbole  启动爬虫  
scrapy shell http://blog.jobbole.com/114690/  //进入scrapy交互界面，方便调试  
 
### 相关语法

#### xpath语法

|表达式     |说明        |
|-----------|-----------|
|article    |选取所有article元素的所有子节点|
|/article   |选取根元素article|
|article/a  |选取所有article的子元素的a元素|
|//div      |选取所有div子元素|
|article//div   |选取所有属于article元素后代的div元素|
|//@class   |选取所有名为class的属性|
|/article/div[1]   |选取属于article子元素的第一个div元素|
|/article/div[last()]   |取属于article子元素的最后一个div元素|
|/article/div[last()-1]   |取属于article子元素的倒数第二个div元素|
|//div[@lang]   |选取所有拥有lang属性的div元素|
|//div[@lang='eng']   |选取所有lang属性为eng的div元素|
|/div/*      |选取属于div元素的所有子节点|
|//*         |选取所有元素              |
|//div[@*]   |选取所有带属性的div元素|
|//div/a|//div/p      |选取所有div元素的a和p元素|