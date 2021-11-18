## urlretrieve(照片url, 檔案名稱)
- 下載url指向的圖片
- urlretrieve(照片url, 檔案名稱)

### 儲存單一照片
```
from urllib.request import urlretrieve
from urllib.request import urlopen
from bs4 import BeautifulSoup

html = urlopen('https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB')
bs = BeautifulSoup(html, 'html.parser')

# 圖片的網址(無https)
Location = bs.find('span', class_ = 'flagicon').find('img')['src'] # .find('img').attrs['src'] 也可以
# 加上 https
print(Location)
imgLocation = "https:{}".format(Location)

# urlretrieve(照片url, 檔案名稱)
# 下載url指向的圖片
urlretrieve(imgLocation, 'TW_flag.png')
```
