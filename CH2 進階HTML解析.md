# 1. find_all 與 find()
- find_all(tag, attributes, recursive, text, limit, keyword)
  - 根據給予的條件去尋找**所有**符合的標籤。 
  - Output 是 **List**
- find(tag, attributes, recursive, text, keyword)
  - 根據給予的條件去尋找**第一個**符合的標籤。 
- find_all('p')[x]
  - x = 0, 即第一個p標籤
  - x = 1, 即第二個p標籤
***
### 1.1 各種條件
- tag
  - .find_all("h1")
  - .find_all(["h1", "h2"])
- tag + attributes
  - .find_all("span", {"class":"nickname"})
  - .find_all("span", {"class":"nickname", "name"})
- keyword (attributes)
  - .find_all(id = "title")
    - = .find_all("", {"id" : "title"}) 
  - .find_all(class_ = "nickname")
    - = .find_all("", {"class" : "nickname"}) 
- text
  - 根據元素內的文字內容搜尋
  - .find_all(text = "Hi")
- recursive
  - 布林值
  - 你想要多深入文件?
  - True
    - 在整個標籤裡尋找符合的標籤 
    - 預設為True
  - False
    - 只在文件的第一層，尋找符合的標籤 
- limit
  - 用於find_all
  - limit = 5
    - 抓5個符合的資料即可
***
# 2. .get_text()
- 取得標籤內的文字。

### 範例一 找出所有 nickname，並且 print 出來。
URL = https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB  
Nick_name = Shao Yi-Me & Mimi Shao
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

# 尋找標籤為span，屬性為class = "nickname" 的元素
all_nick_name = bs_obj.find_all("span", {"class":"nickname"})
```
![img](https://github.com/TonnyLee123/-.md/blob/main/Screenshot%202021-12-08%20175652.jpg)
```python
# 取得標籤內的文字
print(all_nick_name[0].get_text())
print(all_nick_name[1].get_text())

# 使用for loop
for nickName in all_nick_name:
    print(nickName.get_text())
```
# 3. 存取標籤中屬性的值
- img標籤中， src屬性的值。
  - img.attrs['src'] 
  - img.['src'] 
- X.attrs
  - 某標籤的所有屬性清單
  - img.attrs
  - {'href':'~', 'class':'~'} 
### 範例一 找出所有照片的 src
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
web = urlopen("https://pythonscraping.com/pages/page3.html")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

imgs = bs_obj.find_all('img')
for img in imgs:
    # img標籤中， src屬性的值。
    print(img.attrs['src'])
    # 第二種寫法
    print(img['src])
```
# 4.走訪樹(p19)
- 根據位置找元素
- children(子節點)
- descendant(後代)
- bs.body.h1
  - body -> h1
- bs.div.find_all("img")
  - div -> img
```
.children
.next_sibling(s)
.previous_sibling(s)
.parent
```
### 範例一
```python
web = urlopen("https://pythonscraping.com/pages/page3.html")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

for child in bs_obj.find('table', {'id': 'giftList'}).children:
    print(child)
for sibling in bs_obj.find('table', {'id': 'giftList'}).tr.next_siblings:
    print(sibling)
```

### 範例二 讀取某一商品價格
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
***

# 5. regex(regular expression)
- 正規表示式
- 過濾不符合要求的文字，找出符合某格式下的文字!!!
- re.compile('regex)
### 範例一 找出電子郵件地址
```
a91802tony@gmail.com
[A-Za-z0-9\._+]+@[A-Za-z]+\.(com|org|edu|net)
```
### 範例二 找出格式為 \.\./img/gifts/img.*\.jpg 的網址
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
# !注意!
import re
web = urlopen("https://pythonscraping.com/pages/page3.html")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")
# regex
imgs = bs_obj.find_all('img', src = re.compile('\.\./img/gifts/img.*\.jpg'))
for img in imgs:
    print(img)
    print(img['src'])
```


