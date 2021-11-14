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
