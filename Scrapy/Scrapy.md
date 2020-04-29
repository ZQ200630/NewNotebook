## Spiders
```python
1. response.body --str #获取响应数据
2. response.headers --dict #获取响应头
3. response.status --int  #获取状态码
```

## Selector
```python
response.xpath('')  --bytes  #xpath过滤
    xpath().extract()  #返回结果集
        extract()[0]  #返回第一个元素

response.css('') --bytes  #css选择器,同上

response.re('') #其结果不需要格式化
```

## Yield
```python
1. yield scrapy.Response(url=, callback=)  #继续爬取网页数据
2. yield items  #把item送给pipelines处理
```

## Item

```python
scrapy.Field()  #添加字段
```

## Pipeline
```python
处理图片继承ImagesPipeline
    yield scrapy.Response(url)
一般处理json流程:
    def __init__(self):
        self.f = open(fileName, 'wb')
        self.f.write(b'[')

    def process_item(self, item, spider):
        text = json.dumps(dict(item), ensure_ascii=False)
        self.f.write(text.encode('utf-8'))
        self.f.write(b',')
        self.f.write(b'\n')
        return item

    def close_spider(self, spider):
        self.f.seek(-2, os.SEEK_END)
        self.f.write(b']')
        self.f.close()
```

## Settings
```python
BOT_NAME = 'tencent'

SPIDER_MODULES = ['tencent.spiders']
NEWSPIDER_MODULE = 'tencent.spiders'

# Crawl responsibly by identifying yourself (and your website) on the user-agent
#USER_AGENT = 'tencent (+http:#www.yourdomain.com)'  #更改request头, 避免403 Forbidden

# Obey robots.txt rules
ROBOTSTXT_OBEY = True  #是否遵循robots规定,当爬取禁止网站时关闭

# Configure maximum concurrent requests performed by Scrapy (default: 16)
#CONCURRENT_REQUESTS = 32  #Scrapy 一次最大请求数  

# Configure a delay for requests for the same website (default: 0)
# See https:#doc.scrapy.org/en/latest/topics/settings.html#download-delay
# See also autothrottle settings and docs
# DOWNLOAD_DELAY = 3  #下载延时
# The download delay setting will honor only one of:
# CONCURRENT_REQUESTS_PER_DOMAIN = 16
# CONCURRENT_REQUESTS_PER_IP = 16

# Disable cookies (enabled by default)
# COOKIES_ENABLED = False  #设置cookie

# Disable Telnet Console (enabled by default)
# TELNETCONSOLE_ENABLED = False

# Override the default request headers:
# DEFAULT_REQUEST_HEADERS = {
#   'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
#   'Accept-Language': 'en',
# }  #重写默认request头部

# Enable or disable spider middlewares
# See https:#doc.scrapy.org/en/latest/topics/spider-middleware.html
# SPIDER_MIDDLEWARES = {
#    'tencent.middlewares.TencentSpiderMiddleware': 543,
# }  #spider中间件

# Enable or disable downloader middlewares
# See https:#doc.scrapy.org/en/latest/topics/downloader-middleware.html
# DOWNLOADER_MIDDLEWARES = {
#    'tencent.middlewares.TencentDownloaderMiddleware': 543,
# }  #下载器中间件

# Enable or disable extensions
# See https:#doc.scrapy.org/en/latest/topics/extensions.html
# EXTENSIONS = {
#    'scrapy.extensions.telnet.TelnetConsole': None,
#}  

# Configure item pipelines
# See https:#doc.scrapy.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
   'tencent.pipelines.TencentPipeline': 300
}  #pipelines打开

# Enable and configure the AutoThrottle extension (disabled by default)
# See https:#doc.scrapy.org/en/latest/topics/autothrottle.html
#AUTOTHROTTLE_ENABLED = True
# The initial download delay
#AUTOTHROTTLE_START_DELAY = 5
# The maximum download delay to be set in case of high latencies
#AUTOTHROTTLE_MAX_DELAY = 60
# The average number of requests Scrapy should be sending in parallel to
# each remote server
#AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
# Enable showing throttling stats for every response received:
#AUTOTHROTTLE_DEBUG = False

# Enable and configure HTTP caching (disabled by default)
# See https:#doc.scrapy.org/en/latest/topics/downloader-middleware.html#httpcache-middleware-settings
#HTTPCACHE_ENABLED = True
#HTTPCACHE_EXPIRATION_SECS = 0
#HTTPCACHE_DIR = 'httpcache'
#HTTPCACHE_IGNORE_HTTP_CODES = []
#HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage'
```


​        