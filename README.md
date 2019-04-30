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

```
# -*- coding: utf-8 -*-
import scrapy
import re


class JobboleSpider(scrapy.Spider):
    name = 'jobbole'
    allowed_domains = ['blog.jobbole.com']
    start_urls = ['http://blog.jobbole.com/114690/']

    def parse(self, response):
        # 返回符合条件的Selctor的集合SelectorList
        re_selector = response.xpath('//div[@class="entry"]/p/text()')
        # 返回Selector的data数据的集合
        re_value = re_selector.extract()

        reger_date = ".*(\d{4}/\d{2}/\d{2}).*"
        reger_num = ".*?(\d+).*"

        title = response.xpath('//div[@class="entry-header"]/h1/text()').extract_first()
        create_date = str(response.xpath('//p[@class="entry-meta-hide-on-mobile"]/text()').extract_first()).strip()
        match_re = re.match(reger_date, create_date)
        if match_re:
            create_date = match_re.group(1)
        prase_nums = response.xpath('//span[contains(@class, "vote-post-up")]/h10/text()').extract_first()
        fav_nums = response.xpath('//span[contains(@class, "bookmark-btn")]/text()').extract_first()
        match_re = re.match(reger_num, fav_nums)
        if match_re:
            fav_nums = match_re.group(1)
        comment_nums = response.xpath('//a[@href="#article-comment"]/span/text()').extract_first()
        match_re = re.match(reger_num, comment_nums)
        if match_re:
            comment_nums = match_re.group(1)
        pass

```

#### css选择器

|表达式     |说明        |
|-----------|-----------|
|#container      |选择id为container的节点|
|.container     |选择所有class包含container节点|
|li a          |选择所有li下的所有a节点|
|ul + p          |选择ul后面的第一个p元素|
|div#container > ul |选择id为container的div的第一个ul子元素|
|ul ~ p          |选择与ul相邻的所有p元素|
|a[title]          |选择所有有title属性的a元素|
|a[href="jobbole"]   |选择所有href属性为jobbole值的a元素|
|a[href*="jobbole"]   |选择所有href属性包含jobbole值的a元素|
|a[href^="http"]   |选择所有href属性以http开头的a元素|
|a[href$=".jpg"]   |选择所有href属性以.jpg结尾的a元素|
|input[type=radio]:checked  |选择选中的radio的元素|
|div:not(#container)          |选择所有id非container的div属性|
|li:nth-child(3)      |选择第三个li元素 |
|tr:nth-child(2n)     |选择第偶数个tr|

```
# 返回符合条件的Selctor的集合SelectorList
re_selector = response.xpath('.entry p::text')
# 返回Selector的data数据的集合
re_value = re_selector.extract()
```