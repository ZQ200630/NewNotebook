## Scrapy Project
```shell
scrapy startproject myproject [project_dir]  
#创建新项目, 若不想重复生成项目文件夹project_dir可填./

scrapy genspider [options] <name> <domain>
#新建爬虫
#[options]:
#--help, -h              show this help message and exit
#--list, -l              List available templates
#--template=TEMPLATE, -t TEMPLATE
#--force                 If the spider already exists, overwrite it with the
```

## Spider 模板

```python
#CrawlSpider
allow #满足括号中“正则表达式”的值会被提取，如果为空，则会全部匹配。
deny #与这个正则表达式(或正则表达式列表)不匹配的URL一定不提取
allow_domains #会被提取的链接domains。
deny_domains #一定不会被提取链接的domains
restrick_xpaths #使用xpath表达式，和allow共同作用过滤链接
```

## Scrapy Shell

```python
scrapy shell source
	shelp() #打印可用的对象和方法
    fetch(url[, redirect=True]) #爬取新的 URL 并更新所有相关对象
    fetch(request) #通过给定request 爬取，并更新所有相关对象
    view(response) #使用本地浏览器打开给定的响应。这会在计算机中创建一个临时文件，这个文件并不会自动删除
    
    crawler #当前Crawler 对象
    spider #爬取使用的 Spider，如果没有则为Spider对象
    request #最后一个获取页面的Request对象，可以使用 replace() 修改请求或者用 fetch() 提取新请求
    	url(string) #用于请求的URL
        callback(callable) #指定一个回调函数，该回调函数以这个request是的response作为第一个参数。如果未指定callback，则默认使用spider的parse()方法。
        method(string) #HTTP请求的方法，默认为GET（看到GET你应该明白了，过不不明白建议先学习urllib或者requets模块）
        meta(dict) #指定Request.meta属性的初始值。如果给了该参数，dict将会浅拷贝。(浅拷贝不懂的赶紧回炉)
        body(str) #请求消息体
        headers(dict) #request的头信息。
        cookies(dict or list) #cookie有两种格式。
        encoding #url和body参数的编码默认为'utf-8'.如果传入的url或body参数是str类型,就使用该参数进行编码。
	    priority #请求的优先级，默认值为0，优先级高的请求优先下载。
        errback #请求出现异常或出现HTTP错误时（如404页面不存在）的回调函数。
    response #最后一个获取页面的Response对象
    	url #HTTP 响应的url地址，str 类型。
        status #HTTP 响应的状态码，int 类型。
        headers #HTTP 响应的头部，dict 类型。可以调用get或getlist方法对其进行访问。
        body #HTTP 响应正文，bytes 类型。
        text #文本形式的HTTP响应正文，str 类型，它是由 response.body 使用 response.encoding 解码得到的。
        	response.text = response.body.decode(response.encoding)
        encoding #HTTP 响应正文的编码，它的值可能是从HTTP响应头部或正文中解析出来的。
        request #产生该HTTP 响应的Request对象。
        meta #即 response.request.meta, 在构造 Request对象时，可将要传递给响应处理函数的信息通过meta参数传入；响应处理函数处理响应时，通过response.meta 将信息取出。
        selector #Selector 对象用于在Response 中提取数据。
        xpath(query) #使用XPath选择器在Response中提取数据；它是 response.selector.xpath 方法的快捷方式。
        css(query) #使用 CSS选择器在Response中提取数据；它是 response.selector.css方法的快捷方式。
        urljoin (url) #用于构造绝对 url 。当传入的url参数是一个相对地址时，根据response.url 计算出相应的绝对 url。
    settings #当前的Scrapy设置
```

