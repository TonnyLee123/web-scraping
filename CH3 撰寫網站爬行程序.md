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
```python
import requests
from bs4 import BeautifulSoup
class Content:
    def __init__(self, url, title, body):
        self.url = url
        self.title = title
        self.body = body

def getPage(url):
        req = requests.get(url)
        return BeautifulSoup(req.text, 'html.parser')

def scrapeNYTTimes(url):
        bs = getPage(url)
        title = bs.find('h1').text
        lines = bs.find_all('p', {'class':'story-content'})
        body = '\n'.join([line.text for line in lines])
        return Content(url, title, body)

def scrapeBrookings(url):
        bs = getPage(url)
        title = bs.find('h1').text
        body = bs.find('div', {'class' : 'post-body'})
        return Content(url, title, body)


url = 'https://www.brookings.edu/blog/future-development/2018/01/26/delivering-inclusive- urban-access-3-uncomfortable-truths'
content = scrapeBrookings(url)
print('TITLE: {}\n'.format(content.title))
print('URL: {}\n'.format(content.url))
print(content.body)
```
```python
import requests
from bs4 import BeautifulSoup
class Content:
    def __init__(self, url, title, body):
        self.url = url
        self.title = title
        self.body = body

def getPage(url):
        req = requests.get(url)
        return BeautifulSoup(req.text, 'html.parser')

def scrapeNYTTimes(url):
        bs = getPage(url)
        title = bs.find('h1').text
        body = bs.find_all('div', class_ = 'css-s99gbd') # 如何取得新聞內容
        return Content(url, title, body)

def scrapeMimi(url):
        bs = getPage(url)
        title = bs.find('h1').text
        body = bs.find('h')
        return Content(url, title, body)

url = 'https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB'
content = scrapeMimi(url)
print('TITLE: {}\n'.format(content.title))
print('URL: {}\n'.format(content.url))
print(content.body)


url = 'https://www.nytimes.com/2021/11/16/world/asia/biden-xi-usa-china.html'
content = scrapeNYTTimes(url)
print('TITLE: {}\n'.format(content.title))
print('URL: {}\n'.format(content.url))
print(content.body)
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



