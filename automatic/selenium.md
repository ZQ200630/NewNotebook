```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get(url) # 打开网页
driver.find_element(By.ID, id) # 通过ID查找元素
driver.find_element(By.NAME, name) # 通过name查找元素
driver.find_element(By.XPATH, xpath) # 通过xpath查找元素
driver.find_element(By.CLASS_NAME, class_name) # 通过class查找元素
driver.find_element(By.CSS_SELECTOR, selector) # 通过selector查找元素
driver.find_element(By.LINK_TEXT, link) # 通过link查找

script = 'document.getElementById("").click()'
driver.execute_script(script) #运行js脚本
```

### 定位下拉框

```python
from selenium.webdriver.support.ui import Select

element = driver.find_element(By.ID, id)
# 通过属性定位
Select(element).select_by_value(value)
# 通过index定位
Select(element).select_by_index(index)
# 通过可视文本定位
Select(element).select_by_visible_text(text)
```

### 鼠标操作

```python
from selenium.webdriver.common.action_chains import ActionChains
driver = webdriver.Chrome()
element = driver.find_element()
ActionChains(driver).move_to_element(element).perform() # 当需要的操作需要二级菜单时可以先悬停

# 鼠标右键
ActionChains(driver).context_click(element).click()

# 鼠标双击
ActionChains(driver).double_click(element).perform()
```

### 等待

```python
# 强制等待
from time import sleep
sleep(5)

#隐式等待， 浏览器在一段时间不停刷新页面，直到找到元素
driver.implicitly_wait(30)
dr.find_element()

# 显示等待,明确要等到某个元素出现或可点击，如果等不到就一直等下去
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
element = WebDriverWait(driver, 5, 0.5).until(EC.presence_of_element_located((By.ID, 'freename')))
# 5表示最长超时时间，0.5表示检测频率，until表示每隔一段时间调用这个传入方法，直到返回值为True，
EC.presence_of_element_located()表示只要有一个符合条件的元素加载出来就通过
element.send_keys('hello')
```

### iframe处理

```python
driver.switch_to.frame('id or name') #进入iframe
driver.find_element()
driver.switch_to.default_content() #退出iframe

# iframe嵌套则先定位外层iframe再定位内层iframe
# 无论进入几层iframe，只要退出就是最外层

#如果iframe没有明确的id或name，可以利用xpath driver.find_element_by_xpath定位
```

### 切换窗口

```python
windows = driver.window_handles
driver.switch_to.window(windows[1]) # 切换页面
```

### 警告框

```python
alert_text = driver.switch_to.alert.text # 获取警告框文本
print(alert_text)

driver.switch_to.alert.accept() # 确认警告框操作
driver.switch_to.alert.dismiss() # 取消警告框
```

### 浏览器滚动条

```python
driver.execute_script("window.scrollTo(0, 10000)") #从0拖到10000
driver.execute_script("window.scrollTo(10000, 0)") #从10000拖到0
```

### JS

```js
$("selector").style.attribute=""
$("selector").removeAttribute("")
```

### Keys

```python
from selenium.webdriver.keys import Keys
*.send_keys(Keys.DOWN)
```

### 无头模式

```python
chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--headless')
chrome_options.add_argument('--disable-gpu')
driver = webdriver.Chrome(options=chrome_options)
```

### 多线程无头

```python
import threading
thread = threading.Thread(target=function, args=(tuple)) #args必须传入一个元组，如果为int则应该写i,
thread.start()
```

