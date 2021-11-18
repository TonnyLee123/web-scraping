```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re

# set() 元素獨特且沒有特定順序
pages = set()
def getLinks(pageURL):
    global pages
    web = urlopen("https://zh.wikipedia.org{}".format(pageURL))
    html = web.read()
    bs_obj = BeautifulSoup(html, "html.parser")
    
    for links in bs_obj.find_all('a', href=re.compile("^(/wiki/)")):
        if 'href' in links.attrs:
            # 避免爬同一個網站兩次
            if 'href' not in pages:
                newPage = links.attrs['href']
                print(newPage)
                pages.add(newPage)
                # 再爬新的網站
                getLinks(newPage)
getLinks('/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB')
```
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re

# set() 元素獨特且沒有特定順序
pages = set()

def getLinks(pageURL):
    global pages
    web = urlopen("https://zh.wikipedia.org{}".format(pageURL))
    html = web.read()
    bs_obj = BeautifulSoup(html, "html.parser")

    try:
        print(bs_obj.h1.get_text())
        print(bs_obj.find(id = 'mw-content-text').find_all('p')[0])
        print(bs_obj.find(id = 'ca-edit').find('a').attrs['href']))
    except AttributeError:
        print("This page is missing something!")

    for link in bs_obj.find_all('a', href = re.compile('^(/wiki/)')):
        if 'href' in link.attrs:
            if link.attrs['href'] not in pages:
                newPage = link.attrs['href']
                print('-'*20)
                print(newPage)
                pages.add(newPage)
                getLinks(newPage)

getLinks('/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB')
```

上的註解
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup

web = urlopen('https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB')
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

print(bs_obj.h1.get_text())
print(bs_obj.find(id = 'mw-content-text').find_all('p')[0])
print(bs_obj.find(id ='ca-edit').find('a').attrs['href'])

```

### 爬取所有links(包含主題連結，其他連結)
```python
web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

for link in bs_obj.find_all('a'):
    if 'href' in link.attrs:
        print(link.attrs['href'])
```
問題: 擷取到的連結包含主要連結與其他連結(不重要的)

### 爬取主題links
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re

web = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
html = web.read()
bs_obj = BeautifulSoup(html, "html.parser")

for ele in bs_obj.find('div', {'id':'bodyContent'}).find_all('a', href = re.compile("^(/wiki/)((?!:).)*$")):  # 指讀取主題連結
    print(ele.attrs['href'])
```
解決方式: 使用Regex
- Regex 選取合適格式的網址。

wiki主題頁的連結的共同特徵
- 都在bodyContent的div中
- URL 中沒有冒號
- URL 以/wiki/開頭

### getLinks主函式
- 功能: Return bs_object
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re
import random
import datetime

# 以系統目前的時間產收隨機亂數，確保程式每次執行時都有新路徑。
random.seed(datetime.datetime.now())

# getLink():取得bs_object
def getLinks(articleUrl):
    web = urlopen("https://zh.wikipedia.org{}".format(articleUrl)) # https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB
    html = web.read()
    bs_obj = BeautifulSoup(html, "html.parser")
    
    # return vs_obj
    return bs_obj.find('div', id = 'bodyContent').find_all('a', href = re.compile("^(/wiki/)((?!:).)*$")) # 尋找主題連結

subject_links = getLinks('/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB')

# 當頁面有其他主題連結時
while len(subject_links ) > 0:
    # 隨機找出一個主題連結作為新頁面。
    newAriticle = subject_links[random.randint(0, len(subject_links)-1)].attrs['href']
    print(newAriticle)
    subject_links = getLinks(newAriticle) # 新article裡的所有主題連結

```


# 跨整個網站蒐集資料
```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re

# set() 元素獨特且沒有特定順序
pages = set()

def getLinks(pageURL):
    global pages
    web = urlopen("https://zh.wikipedia.org{}".format(pageURL))
    html = web.read()
    bs_obj = BeautifulSoup(html, "html.parser")

    # 有些網址無法編輯(如: 國家, ...), 所以要用try, except
    try:
        # 第一個標題
        print(bs_obj.h1.get_text()) # 標題只有一個所以可以直接.h1
        # 第一段
        # print(bs_obj.p.get_text()) ， !!!當你要第二段就會出現問題
        print(bs_obj.find(id = 'mw-content-text').find_all('p')[0].get_text()) # find_all('p')[0] 第一段
        # 編輯網址
        print(bs_obj.find(id = 'ca-edit').find('a').attrs['href'])
    except AttributeError: # 當遇到AttributeError時，執行下列程式。
        print('This page is missing something!!!')

    for link in bs_obj.find_all('a', href = re.compile('^(/wiki/)')):
        if link.attrs['href'] not in pages:
            newPage = link.attrs['href'] # /wiki/%E6%BC%94%E5%93%A1
            print('-'*20)
            print(newPage)
            getLinks(newPage)

getLinks('/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB')
```
