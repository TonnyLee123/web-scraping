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
```
