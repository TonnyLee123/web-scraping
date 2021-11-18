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
    - = .find_all("", {"id" : "title"}) 
  - .find_all(class_ = "nickname")
    - = .find_all("", {"class" : "nickname"}) 
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









# regex(正規表示式)

### 範例 找出電子郵件地址
```
[A-Za-z0-9\._+]+@[A-Za-z]+\.(com|org|edu|net)
```
- [A-Za-z0-9\._+]+
  - 第一部分: 至少包含下列之一: 大小寫字母，數字，.，_，+
  - []+
  - +
- @
- [A-Za-z]+
- \.
- (com|org|edu)
