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







```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

# 尋找標籤為span 屬性為class = "nickname" 的元素，並且存在名為all_nick_name的list中
all_nick_name = bs_obj.find_all("span", {"class":"nickname"})

print(len(all_nick_name))
print(all_nick_name[0])
print(all_nick_name[1])

print(all_nick_name[0].get_text())
print(all_nick_name[1].get_text())

for nickName in all_nick_name:
    print(nickName.get_text())
# web = urlopen("https://pythonscraping.com/pages/page1.html")
# html = web.read()
# bs_obj = BeautifulSoup(html, "html.parser")

# nameList = bs_obj.find_all("h1")

#for name in nameList:
    #print(name.get_text())
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



## 走訪樹p19
- 根據文件位置找元素
- children(子節點)
- descendant(後代)
- bs.body.h1
  - body標籤的後代的第一個h1標籤 
- bs.div.find_all("img")
  - 找出文件中第一個div，然後在找出此div後代中所有的img

### 範例一 尋找後代的子節點
```
web = urlopen("https://pythonscraping.com/pages/page3.html")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

for child in bs_obj.find('table', {'id': 'giftList'}).children:
    print(child)
for sibling in bs_obj.find('table', {'id': 'giftList'}).tr.next_siblings:
    print(sibling)
```

### 讀取某一商品價格
由下往上
```python
web = urlopen("https://pythonscraping.com/pages/page3.html")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

print(bs_obj.find('img', {'src' : '../img/gifts/img1.jpg'}).parent.previous_sibling.get_text())
```
由上往下
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

web = urlopen("https://pythonscraping.com/pages/page3.html")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

print(bs_obj.find("tr", {"id" : "gift1", "class":"gift"}).td.next_sibling.next_sibling.get_text())
```


```python
web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")
# 產生該網頁的所有連結
# 問題: 擷取到的連結包含主要連結與其他連結
for link in bs_obj.find_all("a"): # <a>的標籤
    if 'href' in link.attrs:
        print(link.attrs['href'])

```
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re

web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

# 解決方式: 使用regex
# 觀察到wiki主題頁的連結的共同特徵
# 都在bodyContent的div中
# URL 中沒有冒號
# URL 以/wiki/開頭
for ele in bs_obj.find('div', {'id':'bodyContent'}).find_all('a', href = re.compile("^(/wiki/)((?!:).)*$")):  # 指讀取主題連結
    print(ele.attrs['href'])
```

```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re
import random
import datetime


random.seed(datetime.datetime.now()) #???

def getLinks(articleUrl):
    web = urlopen("https://zh.wikipedia.org{}".format(articleUrl)) # https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB
    html = web.read()
    bs_obj = BeautifulSoup(html, "html.parser")

    return bs_obj.find('div', id = 'bodyContent').find_all('a', href = re.compile("^(/wiki/)((?!:).)*$"))

subject_links = getLinks('/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB')

while len(subject_links ) > 0:
    newAriticle = subject_links[random.randint(0, len(subject_links)-1)].attrs['href']
    print(newAriticle)      # 把所有article列印出來
    subject_links = getLinks(newAriticle)

```

# Random seed() Method

```python
import random

random.seed(10) # the generator creates a random number based on the seed value, so if the seed value is 10, you will always get 0.5714025946899135 as the first random number.
print(random.random())  # random.random(): 0-1 隨機產生一個數
```
- initialize the random number generator.

- The random number generator needs a number to start with (a seed value), to be able to generate a random number.

- By default the random number generator uses the current system time.

- Use the seed() method to customize the start number of the random number generator.

# datetime 
```python
import datetime

x = datetime.datetime.now() # current date
print(x)
```
[dates](https://www.w3schools.com/python/python_datetime.asp)
