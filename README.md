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
# find_all 與 find()
- 根據不同的屬性找出所需的全部或單一標籤的元素。
- find_all(tag, attributes, recursive, text, limit, keyword)
- find(tag, attributes, recursive, text, keyword)
- tag
  - .find_all("h1")
  - .find_all(["h1", "h2"])
- attributes
  - .find_all("span", {"class":"nickname"})
  - .find_all("span", {"class":"nickname", "name"})
- recursive
  - 布林值
  - 你想要多深入文件?
  - True
    - 在整個標籤裡尋找符合的標籤 
    - 預設為True
  - False
    - 只在文件的第一層，尋找符合的標籤 
- text
  - 根據元素內的文字內容搜尋
  - .find_all(text = "Hi")
- limit
  - 只限於find_all
  - ??? 
- keyword
  - 根據attributes搜尋符合的標籤。
  - .find_all(id = "title")
    - = .find_all("", "id" : "title") 
  - .find_all(class_ = "nickname")
    - = .find_all("", "class" : "nickname") 
## find_all 讀取所有nick name
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

# 尋找標籤為span 屬性為class = "nickname" 的元素
all_nick_name = bs_obj.find_all("span", {"class":"nickname"})

# Shao Yi-Me & Mimi Shao
print(len(all_nick_name)) 
```

2. get_text()
```python
print(all_nick_name[0].get_text())
print(all_nick_name[1].get_text())

# 使用for loop
for nickName in all_nick_name:
    print(nickName.get_text())
```
取得標籤內的文字。


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
