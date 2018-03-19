## 安裝 Python 2

#### Windows ：

- [Python 官網](https://www.python.org/)
- 在 Downloads 標籤裡選擇 2.7.14
- 安裝時記得把 Add Python to PATH 選項勾起來 ， 如不勾之後請自行添加至 PATH 中
- 記得一併安裝 pip

#### Ubuntu ：

Ubuntu 本身已經內建 Python 2 ， 就算版本稍微舊一點也可以

<hr>

## 安裝 pip

#### Windows ：
windows 安裝 python 時會一併安裝 pip

#### Ubuntu ：

ubuntu 雖然內建 python2 但是沒有附贈 pip
輸入指令安裝 pip
```
$ sudo apt-get update
$ sudo apt-get install python-pip
``` 
安裝好後建議更新 pip 至最新版本

<hr>

## Pip 說明

pip 是 python 的套件管理程式 ， 以下為 pip 常用指令
```bash
$ pip install [packagename] # 安裝套件
$ pip install --upgrade pip # 更新 pip
$ pip list # 套件列表
$ pip freeze # 將套件列表以 requirements 格式印出
$ pip freeze > requirements.txt # 套件列表用特定格式輸出到 requirements.txt
$ pip -r requirements.txt # 安裝所有 requirements.txt 中的套件
$ pip install -t lib # 將套件安裝到 lib 資料夾 
```
pip 安裝套件時預設會裝在全部環境底下<br>
如有不同專案用到不同套件 ， 可能導致系統雜亂<br>
因此將利用 virtualenv (虛擬環境)來管理專案套件<br>

<hr>

## Pipenv

安裝 pipenv
```bash
$ pip install pipenv
```
更新 pipenv
```bash
$ pipenv --update
```
pipenv 是 python 官方推薦的套件管理工具 ， 可以用指定的 python 版本處理專案 ， pipenv 同時也內建 virtualenv<br>
詳情參考[pipenv](https://pipenv.readthedocs.io/en/latest/)與[指令列表](https://github.com/pypa/pipenv#-usage)

<hr>

## 建立 virtualenv

安裝好 pipenv 之後就可以建立虛擬環境 ， 解說：
```bash
| global # nothing
| 
| _ _ virtualenv01 # package01
|
|
| _ _ virtualenv02 # package01 、 package02
|
|
| _ _ virtualenv03 # package01 、 package03
```
- 一般執行下 ， 沒有東西可以 import
- 啟動 virtualenv01 ， 有 package01 可以用
- 啟動 virtualenv02 ， 有 package01 跟 package02 可以用

#### 利用 virtualenv 建立虛擬環境
在你想要建立環境的資料夾裡輸入：
```bash
$ virtualenv ./venv # 在當前資料夾底下建立 venv 虛擬環境
```
開起虛擬環境：
```bash
$ venv\scripts\activate # windows
$ source venv/bin/activate # ubuntu
```
會看到 command line 前面出現環境名 ， 之後用 pip 或 pipenv 安裝的東西都會裝在虛擬環境裡 ， 要關閉虛擬環境只要輸入 ：
```bash
$ deactivate
```

#### 利用 pipenv 建立虛擬環境(推薦)

基本指令列表 ：
```bash
$ pipenv --where # 印出專案根目錄
$ pipenv --venv # 印出當前專案所使用的虛擬環境的路徑
$ pipenv --rm # 刪除當前專案所使用的虛擬環境
$ pipenv --three / --two # 使用 python 2 或 3 建立專案 ， 同時會產生 Pipfile 檔案
$ pipenv install # 創建 Pipfile 與 Pipfile.lock 兩個檔案 ， 同時建立虛擬環境 ， 或是安裝 Pipfile.lock 裡的所有套件
$ pipenv install [package-name] # 安裝套件在虛擬環境裡 、 創建 Pipfile與Pipfile.lock 、 建立虛擬環境
$ pipenv python [python-version-or-path-to-python] # 創建指定python版本的虛擬環境
$ pipenv shell # 啟動當前專案的虛擬環境
$ exit / ctrl + d # 離開當前虛擬環境
```
- 但指定的 python 版本必須先安裝好 ， pipenv 不會自動幫你安裝所有版本
- 創建的虛擬環境會統一放在一個預設資料夾底下

pipenv 當前專案判定
```bash
| 
| _ _(Pipfile) A
|
|
| _ _
|    | _ _(Pipfile) B
|    |
|    | _ _(Pipfile) C
|         |
|         | _ _ D
|         |
|         | _ _ E
|
| _ _(Pipfile) F
|    |
|    | _ _ (Pipfile) G
|
| _ _(Nothing) H
```
- A B F G 為獨立專案
- D E (子資料夾) 屬於 C 專案
- G 不屬於 F
- H 什麼都不是
<hr>
利用 pipenv 建立虛擬環境
```bash
$ cd your-project
$ pipenv install
```
會建立一個虛擬環境 ， 同時會建立 Pipfile 與 Pipfile.lock 兩個檔案<br>
這兩個檔案是用來管理套件的 ， 用 pipenv 安裝的東西都會自動列在 Pipfile 裡<br><br>
啟動虛擬環境 ：
```bash
$ pipenv shell
```
如在 windows 系統出現 ... prevent_path_error() SyntaxError: invalid syntax 的問題 ， 輸入下面指令後重新啟動虛擬環境 ：
```bash
$ pip install -e git+https://github.com/pypa/pipenv.git@master#egg=pipenv
```
輸入 exit 或 Ctrl + D 離開虛擬環境
```
$ exit
```

<hr>

## 安裝爬蟲所需套件

安裝 requests
```bash
$ pipenv install requests
```
安裝 selenium
```bash
$ pipenv install selenium
```
安裝 beautifulsoup4
```bash
$ pipenv install beautifulsoup4
```

<hr>

## requests

[request 文件](http://docs.python-requests.org/en/master/)
requests 是 python 用來發出 http request 的套件 ， 之後將利用 requests 將所需要的網頁抓取下來 。

<hr>

## beautifulSoup4

[beautifulSoup4 文件](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
此套件可以分析 html 與 xml 等文件格式 ， 之後將用來抓取所需的特定資料

<hr>

## webdriver

[webdriver 文件](http://selenium-python.readthedocs.io/)
webdriver 可以模擬瀏覽器操作 、 進行滑鼠拖曳 、 打字輸入 、 視窗切換 、 定位元素等行為 。 假設所需資料需經一定操作才能獲取 ， 也可以選擇使用 webdriver。

## HTML

html 是呈現網頁的重要元素 ， 平常由瀏覽器解析並呈現畫面給使用者觀看 ， 而爬蟲則是由 beautifulSoup4 解析並給機器處理 ， 而 html 基本元素如下 ：

```html
<html>
    <head>
    </head>
    <body>
        <div>
            <div id="did" name="dname" class="dclass">text</div>
            text2
        </div>
    </body>
</html>
```

由各種標籤所組成 ， 並且可以擁有各自的屬性（ 如 id 、 name 、 class ）與內容（ 如 text 、 text2 ）