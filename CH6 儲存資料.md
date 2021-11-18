## urlretrieve(照片url, 檔案名稱)
- 下載url指向的圖片
- urlretrieve(照片url, 檔案名稱)

### 儲存單一照片
```python
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

### 儲存資料到CSV
```python
import csv
# 創建一個csv檔案
csvFile = open('test.csv', 'w+')

try:
    # writer(要編輯的檔案)
    # 開啟編輯模式
    writer = csv.writer(csvFile)
    #開始編輯 writerow(): 編輯衡的
    writer.writerow(('number', 'number + 2', 'number + 4'))

    # 加入10行
    for i in range(10):
        writer.writerow((i, i + 2, i + 4))

finally:
    # 退出檔案
    csvFile.close()

```
