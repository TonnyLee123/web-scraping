# web-scraping

urllib
- python標準函式庫
- 功能:
  - 從網路請求資料
  - 處理cookie
  - 處理標頭檔，user agent，metadata 

urlopen
- 打開並讀取HTML檔案，影像檔案，等等

# 執行BeautifulSoup
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

# client與server建立連線
# 打開檔案
web = urlopen("https://edition.cnn.com/business/tech?fbclid=IwAR1GemXmRCnGZUpEKiUcdJmm-Jfg6dDTWWC0c0vzbhvyMvWyW4ipR91gEW8")

# html.read() 取得HTML的內容
# html.parser 建構物件的解析程序
# 另外2種 處理"雜亂"，格式錯亂的HTML
# 1. lxml，html5lib
# 建構beautiful物件
bs = BeautifulSoup(web.read(), "html.parser")
# 法二
# bs = BeautifulSoup(web, "html.parser")

print(bs.h1)
```


# 連線的可靠性與例外處理
```
web = urlopen("https://edition.cnn.com/business/tech?fbclid=IwAR1GemXmRCnGZUpEKiUcdJmm-Jfg6dDTWWC0c0vzbhvyMvWyW4ipR91gEW8")
```
此指令可能發生2種主要的問題
- 找不到server
- 在server找不到，找錯，讀取錯誤這個網頁
  - HTTPError 
    - 404 PageNot Found
    - 500 Internal Server Error

處理此例外





```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
print(html) 
```


















## 讀取並整理HTML檔
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

print(bs_obj)
```

## 讀取bs_obj裡的標籤
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

print(bs_obj.h1) #讀取標籤為h1的元素
```

## find_all 讀取所有nick name
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

# 尋找標籤為span 屬性為class = "nickname" 的元素
all_nick_name = bs_obj.find_all("span", {"class":"nickname"})

print(len(all_nick_name))
```
