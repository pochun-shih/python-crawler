# 利用 webdriver 自動選擇並搜尋

Webdriver 不一樣的地方是它可以模擬各種操作行為 ， 直接操作各項 html  元素。這邊將對 mobile01 進行自動搜尋並將搜尋結果中的標題存下來 。

## 表達文件編碼
在文件開頭 ：
```python
# coding=utf-8
```

## import 套件

```python
import unittest
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from bs4 import BeautifulSoup
import time
```

## unittest

unittest 是 python 內建的簡易測試框架 使用方法如下
```python
# coding=utf-8

import unittest

class TestClass(unittest.TestCase):

    def setUp(self):
        print('start test')

    def test_function1(self):
        print('start first test')

    def test_function2(self):
        print('start second test')

    def test_function3(self):
        print('start third test')

    def tearDown(self):
        print('test over')

if __name__ == "__main__":
    unittest.main()
```

- setUp 開始測試時會呼叫
- tearDown 結束測試時會呼叫
- test_yourFunctionName 要測試的功能

要測試的功能一定要以 test_ 開頭 ， 此範例會測試三個功能 ， 即 setUp 與 tearDown 各會呼叫三次 ， 順序為 setUp() => test_function1() => tearDown() => setUp() => ...

## 加入測試

```python
# coding=utf-8

class TestMobile01(unittest.TestCase):

    def setUp(self):

    def test_search(self):

    def tearDown(self):

if __name__ == "__main__":
    unittest.main()
```

## 初始化 webdriver

在 setUp 裡添加
```python
self.driver = webdriver.Firefox()
```
這邊將選擇 firefox 瀏覽器進行稍後的操作。<br>
除了 firefox 還有[其他選擇](http://selenium-python.readthedocs.io/api.html) ， 除了指定瀏覽器外還要將該[瀏覽器的驅動](http://selenium-python.readthedocs.io/installation.html#drivers)放到系統路徑裡 ， 而用常用的瀏覽器執行時都會開啟瀏覽器呈現自動操作畫面 ， 假如希望隱藏執行畫面在背景執行 ， 可以選擇 [Phantom js](http://phantomjs.org/) 。

## 關閉 webdriver

在 tearDown 裡添加
```python
self.driver.close()
```
呼叫 close() 會將開啟的瀏覽器關閉

## 利用 webdriver 搜尋

開啟 mobile01 搜尋頁面 ： 在 test_search 裡添加
```python
driver = self.driver
driver.get("https://www.mobile01.com/googlesearch.php")
```
<br>
接著要選擇分類 ， 搜尋頁面裡的 select 主分類標籤 id 為 c ， 次分類標籤 id 為 f ， 並且旅遊美食的 value 為 18 ， 台中市的 value 為 195 ， 所以接著添加如下程式碼 ：
```python
selectA = driver.find_element_by_xpath("//select[@id='c']")
optionA = selectA.find_elements_by_tag_name("option")
for option in optionA:
    if(option.get_attribute("value") == '18'):
        option.click()

selectB = driver.find_element_by_xpath("//select[@id='f")
optionB = selectB.find_elements_by_tag_name("option")
for option in optionB:
    if(option.get_attribute("value") == '195'):
        option.click()
```
<br>
就會搜尋旅遊美食-台中分類 ， 然後要輸入搜尋字串 ， 接著添加程式碼 ：
```python
search = driver.find_element_by_xpath("//input[@name='search']")
search.send_keys(u'雞排')
search.send_keys(Keys.RETURN)
```
這段程式碼會定位輸入框並輸入"雞排"二字 ， 然後再輸入[特殊字元](http://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.common.keys) Enter 鍵送出搜尋
<br>
利用 BeautifulSoup 分析搜尋結果 ：
```python
soup = BeautifulSoup(driver.page_source, 'html.parser')
titles = soup.find_all("td", class_="gsc-table-cell-snippet-close")
```
這邊將搜尋結果的原始碼給 BeautifulSoup 分析 ， 並取得每個搜尋結果
<br>
取標題文字並寫檔案 ： 
```python
with open(u'雞排.txt', 'w') as f:
    for title in titles:
        text = title.find("a", class_="gs-title").text.encode('utf-8')
        f.write(text + '\n')
```

## 帶參數搜尋

希望能夠執行時附加參數 ， 不必透過修改程式碼更改搜尋目標。如：
```
$ python search.py 珍奶
```
<br>
先 import sys ：
```python
import sys
```
<br>
因此在 ``if __name__ == "__main__":`` 底下新增 ：
```python
if len(sys.argv) > 1:
    TestMobile01.something = sys.argv.pop()
```
<br>
在 Class 底下新增預設值 ：
```python
class TestMobile01(unittest.TestCase):
    something = u'雞排'
```
<br>
在測試案例中取得值 ：
```python
def test_search(self):
    something = self.something
```
<br>
修改搜尋框輸入值　：
```python
search.send_keys(something.decode('big5'))
```
因為 windows cmd 預設編碼為 big5 ， 所以要將它變成 unicode 編碼
<br>
修改存檔名 ：
```python
with open(something.decode('big5') + '.txt', 'w') as f:
```
同上 ， 改成 unicode 編碼

## 整體
```python
# coding=utf-8

import unittest
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from bs4 import BeautifulSoup
import time
import sys

class TestMobile01(unittest.TestCase):
    something = u'雞排'

    def setUp(self):
        # 選擇火狐瀏覽器
        self.driver = webdriver.Firefox()

    def test_search(self):
        # 開啟搜尋頁
        driver = self.driver
        something = self.something
        driver.get("https://www.mobile01.com/googlesearch.php")

        # 定位主分類 select 並點擊旅遊美食(18)選項
        selectA = driver.find_element_by_xpath("//select[@id='c']")
        optionA = selectA.find_elements_by_tag_name("option")
        for option in optionA:
            if(option.get_attribute("value") == '18'):
                option.click()

        # 定位次分類 select 並點擊旅遊美食(195)選項
        selectB = driver.find_element_by_xpath("//select[@id='f']")
        optionB = selectB.find_elements_by_tag_name("option")
        for option in optionB:
            if(option.get_attribute("value") == '195'):
                option.click()

        # 定位搜尋輸入框並輸入搜尋文字 ， sleep 是為了觀看成果所以暫停
        search = driver.find_element_by_xpath("//input[@name='search']")
        search.send_keys(something.decode('big5'))
        time.sleep(3)
        search.send_keys(Keys.RETURN)

        # 分析並取得每個標題
        soup = BeautifulSoup(driver.page_source, 'html.parser')
        titles = soup.find_all("td", class_="gsc-table-cell-snippet-close")

        # 寫檔
        with open(something.decode('big5') + '.txt', 'w') as f:
            for title in titles:
                # 定位標題
                text = title.find("a", class_="gs-title").text.encode('utf-8')
                f.write(text + '\n')

    def tearDown(self):
        # 關閉 webdriver
        self.driver.close()

if __name__ == "__main__":
    # 假如有帶參數
    if len(sys.argv) > 1:
        # 搜尋目標 = 參數
        TestMobile01.something = sys.argv.pop()
    # 開始測試
    unittest.main()
```