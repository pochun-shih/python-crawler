# 利用爬蟲抓取圖片

## 抓取 imgur

#### urllib

urllib 在較高的 python 版本中內建 ， 不需額外安裝。
<br>
urllib 與 requests 相似 ， 可以對外發出請求 。
<br>
urllib3 在安裝 requests 時會一併安裝
<br>
最常用的有 urlopen() 與 urlretrieve() 方法 ， 前者為發出 http 請求 ， 後者為將網路資源取回 ， 如抓取圖片。
<br>
[urllib 文件](https://docs.python.org/3/library/urllib.html)(python 3)
<br>
[範例圖片](https://i.imgur.com/GuHulMWg.jpg)


表達文件編碼
```python
# coding=utf-8
```

import 套件
```python
import urllib.request
```

抓取圖片
```python
urllib.request.urlretrieve("https://i.imgur.com/GuHulMWg.jpg", "dog.jpg")
```

## 整體

```python
# coding=utf-8

import urllib.request

urllib.request.urlretrieve("https://i.imgur.com/GuHulMWg.jpg", "dog.jpg")
```

<hr>

## 抓取 pixiv 圖片

pixiv 網站是插畫交流網站 ， 內含大量的作畫 ， 而 pixiv 有做一些防爬蟲機制 ， 防止爬蟲機器人取得圖片 。

- 看圖限制： 沒有登入則無法查看大圖
- 登入限制： 登入不只需要帳號密碼
- 大圖限制： 查看大圖要帶 header 參數

基本上遇到如上困難 ， 所以要抓取 pixiv 的圖片必須要登入 、 知道登入需要哪些資訊 、 查看大圖需要哪些資訊 。

## 程式部分

##### 表達文件編碼與 Import 套件

```python
# coding=utf-8

import requests
from bs4 import BeautifulSoup
import time
```

##### 定義登入網頁 ， 登入網頁 Api

```python
loginPage = 'https://accounts.pixiv.net/login?lang=zh_tw&source=pc&view_type=page&ref=wwwtop_accounts_index'
postPage = 'https://accounts.pixiv.net/api/login?lang=zh_tw'
```
loginPage 為登入頁
<br>
postPage 為登入時會 post 的網址
<br>

##### 定義表頭與資料

```python
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.162 Safari/537.36'
}

payload = {
    'pixiv_id': 'pixiv_id',
    'password': 'password',
    'source': 'pc',
    'ref': 'wwwtop_accounts_index',
    'return_to': 'https://www.pixiv.net/'
}
```
<br>

##### 建立 requests

```python
s = requests.Session()
s.headers = headers
```
<br>

##### 取得登入頁面的 post_key

```python
r = s.get(loginPage)
soup = BeautifulSoup((r.text), 'html.parser')
post_key = soup.find('input', attrs={'name': 'post_key'})['value']
payload['post_key'] = post_key
```
<br>

##### 登入 pixiv

```python
l = s.post(postPage, data=payload)
```
用 post 方法並帶登入資訊
<br>

##### 取得目標畫廊 ， 儲存圖片網址

```python
head = 'https://www.pixiv.net'
url = []
for li in lis:
    url.append( li.a["href"] )

# work like a human
time.sleep(1)
```
<br>

##### 點進去每張圖片

```python
for i, u in enumerate(url):
    j = head + u
    p = s.get(j)
    page = BeautifulSoup((p.text), 'html.parser')
    src = page.find('img', class_='original-image')['data-src']

    # work like a human
    time.sleep(2)
```
大圖網址屬性分析出來為 data-src ， 不是 src
<br>

##### 下載每張圖片

```python
    s.headers['referer'] = src
    img = s.get(src, stream=True)
    with open('img0'+str(i)+'.jpg', 'wb') as f:
        for chunk in img:
            f.write(chunk)
            if (not chunk):
                break
```
<br>

## 整體

```python
# coding=utf-8

import requests
from bs4 import BeautifulSoup
import time

# login-page and login api
loginPage = 'https://accounts.pixiv.net/login?lang=zh_tw&source=pc&view_type=page&ref=wwwtop_accounts_index'
postPage = 'https://accounts.pixiv.net/api/login?lang=zh_tw'

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.162 Safari/537.36'
}

payload = {
    'pixiv_id': 'pixiv_id',
    'password': 'password',
    'source': 'pc',
    'ref': 'wwwtop_accounts_index',
    'return_to': 'https://www.pixiv.net/'
}

# create requests
s = requests.Session()
s.headers = headers

# get post-key
r = s.get(loginPage)
soup = BeautifulSoup((r.text), 'html.parser')
post_key = soup.find('input', attrs={'name': 'post_key'})['value']
payload['post_key'] = post_key

# login
l = s.post(postPage, data=payload)

# get gallery
t = s.get('https://www.pixiv.net/member_illust.php?id=366411')
gallery = BeautifulSoup((t.text), 'html.parser')
lis = gallery.find_all('li', class_='image-item', limit=2)

# get urls
head = 'https://www.pixiv.net'
url = []
for li in lis:
    url.append( li.a["href"] )

time.sleep(1)

# go every image
for i, u in enumerate(url):
    # get source image url
    j = head + u
    p = s.get(j)
    page = BeautifulSoup((p.text), 'html.parser')
    src = page.find('img', class_='original-image')['data-src']
    print('find: ' + src)
    time.sleep(2)

    # download image
    s.headers['referer'] = src
    img = s.get(src, stream=True)
    with open('img0'+str(i)+'.jpg', 'wb') as f:
        for chunk in img:
            f.write(chunk)
            if (not chunk):
                break
```