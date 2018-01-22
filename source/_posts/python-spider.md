---
title: python爬虫
tags:
  - python
categories:
  - 后端
date: 2018-01-22 14:31:19
---

## Python的安装

#### Python的版本问题

Python3的版本还不够稳定，网上的许多第三方库还无法支持，故而选择Python2.x


#### Python的下载

 国外的网站比较慢，直接用别人的云盘可以快速下载 [国内镜像](https://pan.baidu.com/s/1kU5OCOB#list/path=%2Fpub%2Fpython)

 注意勾选**pip**和**Add python.exe to Path**，然后一路傻瓜安装

#### Py文件运行时控制台中文乱码

在文件的最开始设置编码规则

    #coding=utf-8

在需要输出的地方解码 **.encode('utf-8')**     

     fout.write('<td class="summary">%s</td>'% data['summary'].encode('utf-8'))

或者引入第三方

    #设置编码  
    import sys 
    reload(sys)  
    sys.setdefaultencoding('utf-8')  
    #获得系统编码格式  
    type = sys.getfilesystemencoding() 

并在需要打印中文的地方解码 **.decode('utf-8').encode(type)**

    print '获取lacie连接'.decode('utf-8').encode(type)

## 爬虫程序的组成

#### 爬虫的调度程序

整个爬取过程的程序主入口 **spider_main.py**

    #引入相关模块 
    import url_manager,html_downloader,html_parser,html_outputer
    #实例化相关模块 
    urls = url_manager.UrlManager()
    downloader = html_downloader.HtmlDownLoader()
    parser = html_parser.htmlParser()
    outputer = html_outputer.htmlOutputer()

在引入下列相关模块(例如html下载和解析等),一定要在定义**不要在函数内实例化**相关模块

#### url管理器 

1、集合python set() 函数创建一个无序不重复元素集old_urls和new_urls

2、判断是否有新的url，如果有且同时不在老地址库old_urls时，就用add(url)加入到new_urls这个数组

3、从new_urls这个新地址库获取新的地址url，同时把这个拿出来的地址放入到旧的地址库old_urls

#### html下载器

使用python自带的模块**urllib2** 

引入相关模块

    import urllib2

方式1

    response1 = urllib2.urlopen(url)

方式2 **http头**

    使用Request添加或修改http头
    Accept:application/json
    Content-Type:application/json
    User-Agent:Chorme

    headers={'User-Agent':'Mozilla/5.0','x-my-header':'my value'}
    request = urllib2.Request(url)
    request.add_header(headers)
    response2 = urllib2.urlopen(request)

方式3 **cookies处理**    
    import cookielib
    #声明一个CookieJar对象实例来保存cookie
    cj = cookielib.CookieJar()
    #利用urllib2库的HTTPCookieProcessor对象来创建cookie处理器
    handler=urllib2.HTTPCookieProcessor(cj)
    #通过handler来构建opener
    opener = urllib2.build_opener(handler)
    urllib2.install_opener(opener)
    response3 = urllib2.urlopen(url)

HTML下载器的相关知识可参考 [urllib2相关知识](http://blog.csdn.net/pipisorry/article/details/47905781)

当 *response.getcode() == 200* 时返回数据 response.read() 否则返回空

    if response.getcode() != 200:
              return None

          return response.read() #返回数据

#### HTML解析器

我们使用BeautifulSoup来解析HTML,操作相关dom等。

首先引入相关模块

    from bs4 import BeautifulSoup
    import urlparse
    import re
    #html_cont是html下载器返回的结果
    soup = BeautifulSoup(html_cont,'html.parser',from_encoding='utf-8') 

再结合使用**soup.find_all()**方法找到相关节点并获取文本等

    title_node = soup.find('h1',class_='post-title') #找到对应节点
    res_data['title'] = title_node.get_text()   #获取文本

[中文官网地址](http://beautifulsoup.readthedocs.io/zh_CN/v4.4.0/)

#### 结果以html形式输出

    #文件输出对象 w写模式
    fout = open('output.html','w')  
    fout.write('<html>')
    fout.write('<head>')
    fout.write('</head>')
    fout.write('<body>')
    fout.write('<div><p>%s</p></div'% data['url'])
    fout.write('<div class="title">%s</div>'% data['title'].encode('utf-8'))
    fout.write('<div class="summary">%s</div>'% data['summary'].encode('utf-8'))
    fout.write('</body>')
    fout.write('</html>')

    #关闭输出
    fout.close()
