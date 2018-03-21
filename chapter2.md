# 抓取 PTT 網頁版

[ptt 網頁版](https://www.ptt.cc/bbs/hotboards.html)

## 表達文件編碼
在文件開頭 ：
```python
# coding=utf-8
```
在 python2 中如果不表達編碼 ， 程式內又包含中文等其他自元 ， 則會編譯失敗 。

## import 套件

```python
import requests
from bs4 import BeautifulSoup
```
## 用 requests 抓取目標頁面

```python
r = requests.get('https://www.ptt.cc/bbs/hotboards.html')
```

## 用 BeautifulSoup 解析抓取的 html

```python
soup = BeautifulSoup((r.text), 'html.parser')
```
BeautifulSoup 有多種文件解析器 ， html.parser 是 python 的基本內建解析器

| 解析器           | 使用方法                                                            | 優點                                                    | 缺點                                  |
|------------------|---------------------------------------------------------------------|---------------------------------------------------------|---------------------------------------|
| Python標準庫     | BeautifulSoup(markup, "html.parser")                                | Python的內建標準庫 執行速度適中 文檔容錯率良好          | python 2.7.3 / 3.2.2 之前中文容錯率差 |
| lxml HTML 解析器 | BeautifulSoup(markup, "lxml")                                       | 速度快 文檔容錯率良好                                   | 需要安裝 C 語言                       |
| lxml XML 解析器  | BeautifulSoup(markup, ["lxml", "xml"]) BeautifulSoup(markup, "xml") | 速度快 唯一支持XML的解析器                              | 需要安裝 C 語言                       |
| html5lib         | BeautifulSoup(markup, "html5lib")                                   | 最好的容錯性 以瀏覽器的方式解析文檔 生成HTML5格式的文檔 | 速度慢 不依賴外部擴展                 |

## 如何定位目標資料

BeautifulSoup 解析完成後將會變成節點樹 ， 並有許多方法定位目標節點 ， 方法與功能在[官方文件](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)中寫得非常詳細
<br>
在 ptt 網頁版中可以看到目標被一個 ``class="b-list-container action-bar-margin bbs-screen"`` 的 div 包圍住 ， 所以我們可以利用
```python
soup.find("div", class_="b-list-container action-bar-margin bbs-screen")
```
將整個目標節點取出 ， 並且每一列都是被 class="b-ent" 的 div 包圍住 ， 利用
```python
div.find_all("div", class_="b-ent")
```
可以取得所有 class 為 "b-ent" 的資料 ， 傳回的值為 array ， 而在每一列當中， 又有一個 a 標籤包住整列 ， 這個 a 標籤包含了該版的網址 ， 採用
```python
this_node.a["href"]
```
獲取網址 ， 而 a 標籤裡各個文字的 class 分別為 ：
- 版名 ： board-name
- 標題 ： board-title
- 進版文字 ： board-class
- 人氣 ： board-nuser
不過人氣實際上還被包在子節點中的 span 標籤中
```python
this_node.span.text
```
所以用上面的方法拿文字

## 寫檔案

取得全部列
```python
div = soup.find("div", class_="b-list-container action-bar-margin bbs-screen")
```
開檔 寫標題
```python
with open('ptt.txt', 'w', encoding="utf-8") as f:
    f.write("{:20}{:20}{:20}{:20}{:20}\n".format("版名", "人氣", "類別", "進版文字", "網址"))
```
利用 for 迴圈取得每一個版
```python
    for child in div.find_all("div", class_="b-ent"):
```
拿內容文字並輸出至檔案
```python
        url   = child.a["href"]
        name  = child.find("div" , class_="board-name").text
        title = child.find("div" , class_="board-title").text
        ctype = child.find("div" , class_="board-class").text
        count = child.find("div" , class_="board-nuser")
        c     = count.span.text
        coco  = "{:20}{:20}{:20}{:20}{:20}\n".format(name, c, title, ctype, url)
        f.write(coco)
```

## 整體

```python
# coding=utf-8

import requests
from bs4 import BeautifulSoup

# get html
r = requests.get('https://www.ptt.cc/bbs/hotboards.html')
# 讓 BeautifulSoup 解析
soup = BeautifulSoup((r.text), 'html.parser')

# 尋找目標
div = soup.find("div", class_="b-list-container action-bar-margin bbs-screen")
# 寫檔
with open('ptt.txt', 'w', encoding="utf-8") as f:
    # 寫標題
    f.write("{:20}{:20}{:20}{:20}{:20}\n".format("版名", "人氣", "類別", "進版文字", "網址"))
    # 將每一個版逐一寫出
    for child in div.find_all("div", class_="b-ent"):
        # 取得所需文字
        url   = child.a["href"]
        name  = child.find("div" , class_="board-name").text
        title = child.find("div" , class_="board-title").text
        ctype = child.find("div" , class_="board-class").text
        count = child.find("div" , class_="board-nuser")
        c     = count.span.text
        # 合併成一行
        coco  = "{:20}{:20}{:20}{:20}{:20}\n".format(name, c, title, ctype, url)
        # 寫檔
        f.write(coco)
```