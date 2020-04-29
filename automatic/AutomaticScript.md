### pyperclip
```python
copy()
paste()
```

### sys
```python
argv  #命令行参数
exit()  #退出程序
```

### os
```python
mkdirs()  #创建文件夹
path()
    abspath(path)  #返回path的绝对路径
    isabs(path)  #判断path是否为绝对路径
    relpath(path, start)  #返回start到path的相对路径
    basename(path)  #返回path最后一个/后面的内容
    dirname(path)  #返回path最后一个/前的内容
    split()  #返回上述两个方法所获得的元组
    getsize(path)  #返回path文件字节数
    exists(path)  #检查是否存在文件或文件夹
    isfile(path)  #价差是否为文件
    isdir(path)  #检查是否为文件夹
listdir(path)  #列出所有文件
unlink(path)  #删除path处文件
    rmdir(path)  #删除path处文件夹(文件夹必须为空)
walk(path)  #遍历目录树，返回值分别为文件夹名称字符串，文件夹中子文件夹的字符列表，当前文件夹中文件的字符串的列  
```

### shelve
```python
open(name)  #创建变量保存文件
file[''] = ...
```

### shutil
```python
copy(source, destination)  #把source处文件拷贝到destination处
copytree(source, destination)  #拷贝source文件夹
move(source, destination)  #将source处文件移动到destination
rmtree(path)  #删除文件夹及其文件
```

### send2trash
```python
send2trash(path)  #把文件送入回收站
```

### zipfile
```python
ZipFile(path)  #读取一个zip文件
    namelist()  #返回zip文件中的文件列表
    extractall()  #  #解压缩所有文件
    extract(name, path)  #解压缩某文件至某位置
    close()  #关闭文件
    getinfo(name)  #返回某一文件信息
        file_size  #文件大小
        compress_size #压缩后大小
ZipFile(name, 'w')  #以写模式打开zip对象,若追加写入则加一个a
    write('filename', compress_type=zimfile.ZIP_DEFLATED)
```

### 抛出异常
```python
raise Exception(message)  #抛出一个自定义异常
```

### 断言
```python
assert #条件判断语句, 当条件为false时显示的字符串
#运行python时传入-O选项可以禁用断言
```

### logging
```python
logging.basicConfig(level=logging.DEBUG, format=' %(asctime)s - %(levelname)s -%(message)s')  #创建一个logrecord对象，保存关于该事件细节，level表示最低产生日志的等级
debug(message)  #按照上述格式打印debug
disable(logging.级别)  #不打印某一级别的信息
```


|  Level   |      Function      |                    Describe                    |
| :------: | :----------------: | :--------------------------------------------: |
|  DEBUG   |  logging.debug()   |             诊断问题时关心的小细节             |
|   INFO   |   logging.info()   |     记录程序中的一般时间信息，确保一切正常     |
| WARNING  | logging.warning()  | 表示可能的问题，不会阻止程序工作，但将来可能会 |
|  ERROR   |  logging.error()   |          记录错误，导致程序做某事失败          |
| CRITICAL | logging.critical() |                  表示致命错误                  |


### webbroser
```python
open(url)  #启动新浏览器打开URL
```

### selenium
```python
from selenium import webdriver
browser = webdriver.Chrome()
browser.get(url)
click()  #点击页面
send_keys('')  #向input发送文本
submit()  #在任意input文本中调用此方法都会提交该表单
forward()  #点击前进按钮
refresh()  #点击刷新按钮
quit()  #点击退出按钮
```
<center>WebElement的属性和方法</center>
Attribute & Function | Describtion
---|---
tag_name| 标签名
get_attribute(name)|该元素name属性的值
text|返回该元素的文本
clear()|对文字或文本可见区域，清除其中输入的文本
is_displayed()|略
is_enabled()|略
is_selected()|对复选框或单选框元素，元素被选中返回True
location|一个包含'x','y'的字典，表示在页面上的位置

<center>特殊键</center>
Attribute | Meaning
---|---
Keys.DOWN, Keys.UP, Keys.LEFT, Keys.Right | 键盘箭头键
Keys.ENTER,Keys.RETURN | 回车和换行键
Keys.HOME,Keys.END,Keys.PAGE_DOWN,Keys.PAGE_UP|Home，End，PageUP， PageDown
Keys.ESCAPE, Keys.BACK_SPACE, Keys.DELETE | Esc, backspace 和删除键
Keys.F1..... | F1-F12键
Keys.TAB | Tab键

### openpyxl
```python
openpyxl.load_workbook('example.xlsx')
    get_sheet_names()  #获取所有的sheet名
    get_active_sheet()  #取得活动工作表，即打开的为excel打开时直接出现的
    sheet = get_sheet_by_name(name)  #得到一个sheet对象
            title  #此sheet名
        sheet[cellname]  #返回cell
            value  #返回该cell数据
            row  #返回该cell行号
            column  #返回该cell列号
            coordinate  #返回该cell行与列
        sheet['A1':'A3']  #获取此整个矩形区域的cell元素
        tuple(sheet['A1':'A3'])  #在一个元组中列出cell对象
        sheet.columns[number]  #返回一列
        sheet.cell(row=..., column=...)  #返回cell
        sheet.row_dimensions[number].height = 70  #将第一行高度设置为70
        sheet.column_dimensions['B'].width = 20  #将B列宽度设置为20
        sheet.merge_cells('A1:D3')  #把该矩形合并
        sheet.unmerge_cells(‘A1:d3’)  #把该矩形拆分
        sheet.freeze_panes='param'  #param单元格左边与上边所有元素将会冻结
        sheet.freeze_panes='A1'/None  #没有冻结窗口
    get_highest_column()  #返回最大列
    get_highest_row()  #返回最大行
    save(filename)  #保存更改
    Workbook()  #新建一个工作空间
        create_sheet()  #新建默认表
        create_sheet(index=..., title=...)  #索引与名字
        remove_sheet(wb.get_sheet_by_name(name))  #删除sheet
        sheet['A1'] = '......'  #将值写入单元格
        
openpyx1.cell
    get_column_letter(number)  #返回列字母组合
    column_index_from_string('')  #返回字符串对应的列数

from openpyx1.styles import Font, Style
    fonObj1 = Font(name='fontname', bold=True, size=24, italic=True)  #生成一个fontname字体，加粗，大小为24，斜体的字体对象
    sheet['A1'].font = fonObj1  #应用字体
    sheet['A3'] = 'SUM(A1:A2)'  #使用公式求和(若想让sheet使用该公式，则在load_workbook中加一个参数data_only=True)
```


​        
### PyPDF2
```python
pdfFileObj = open('path', 'rb')  #通过二进制格式打开一个pdf文档
reader = PyPDF2.PdfFileReader(pdfFileObj)  #读入该pdf对象
reader.getPageNumber()  #返回该pdf对象页数
page = reader.getPage(50)  #返回第五十页pdf
page.extractText()  #返回提取出来的文字字符串
page.rotateClock(angle)  #将page旋转一个角度
page.mergePage(page1)  #将page1叠到page上
reader.isEncrypted  #是否加密了(若加密无法读取文件)
reader.decrypt(password)  #通过password解锁文件,返回值是否解锁   
Copy pages：
    writer = PyPDF2.PdfFileWriter()
    writer.addPage(page)  #在writer尾部添加一个页面
    writer.write(file)  #把更改的writer写入某个pdf文件
    writer.encrypt(password)  #加密pdf文档
```

### docx
```python
doc = docx.Documen('name')  #新建一个文档对象
len(doc.paragraphs)  #返回段落数
doc.paragraphs[0].text  #返回documentTitle，其后调用返回文本字符串
    runs[number].text  #返回每一个runs的text
```

### csv
```python
reader = csv.reader(file)  #读入一个csv文件，reader是可以遍历的，结果是一行的列表
data = list(reader)  #把文件解析
datat[num][num]  #读取某行某列数据
```


​    
### time
```python
time()  #返回Unix纪元参考时间
sleep()  #使程序暂停
round(数值， 精度)  #返回精度的数值
```


​    
### datetime
```python
datetime.now()  #返回datetime对象，包含当前日期和时间
datetime(2015, 10, 21, 16, 29, 0)  #传入年月日时分秒构造一个时间对象,分别保存在year, month, day, hour, minuite, second中
datetime.datetime.fromtimestamp(seconds)  #返回Unix纪元后的seconds秒的时间对象
         strftime('%Y/%m/%d $H:%M:%S')  #格式化datetime返回字符串
timedelta(days=, hours=, minutes=, seconds= )  #创建一个储存时间的对象，其可以与上述的datetime相加返回一个新的datetime
datetime.strptime('string'， format)  #通过format从string中初始化datetime
Palse to accurate date：
    date = datetime.datetime(.....)
    while datetime.datetime.now() < date:
        time.sleep(1)
```

### threading
```python
Thread(target=def, args=[], kwargs={key:value})  #新建一个调用def的线程,参数为args,关键词用kwargs
    start()  #启动该线程
```

### subprocess
```python
Popen('programName')  #通过程序名开启外部程序
    poll()  #如果外部程序执行完毕，则返回0,否则返回None
    wait()  #停止程序执行，知道外部程序执行完毕
Popen([])  #传递命令行参数，该列表第一个参数为程序名，后面全部都是命令行参数
Popen([]， shell=True)  #利用默认方式打开
```
### Task Scheduler / Launchd / Cron
    均可以分配计算机时间做事

### smtplib
```python
smtpObj = smtplib.SMTP('smtp.qq.com', 25)  #指定smtp服务器
smtpObj.ehlo()  #像服务器发起连接请求
smtpObj.starttls()  #TLS加密
smtpObj.login('2814220624@qq.com', 'cdpfjjsuiydudcfc')  #登录
print(smtpObj.sendmail('2814220624@qq.com','m17607103874@163.com', 'Subject: Lont time no see'))  #发送邮件
smtpObj.quit()  #退出  
```
