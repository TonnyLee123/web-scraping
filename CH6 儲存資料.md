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

# CSV
- Comma-separated values
- Python standard library

## 常用function()
- open()
- close()
- csv.writer()
- writerow()
- writerows()

### 1. 儲存資料到CSV
```python
import csv

# 若檔案存在，則開啟檔案
# 若檔案不存在，則新增檔案
# newline='' 不換行

csvFile = open('test.csv', 'w', newline='')

# 將檔案存入csv物件中
# 進入寫模式
csv_obj  = csv.writer(csvFile)

# 寫入第一行資料
csv_obj.writerow(['name', 'age', 'phone'])

# 寫入多行資料
# 資料都要放在[]裡
data = [
	('Tonny', '20', '09123456789')  # 第一行
	('Amy', '18', '09123456788')    # 第二行
 ]
csv_obj.writerow(data)

# 關閉csv物件
csv_File.close()
```
### 2. 儲存資料到CSV
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

### 3. 讀取csv檔案
- reader
    - 以列表形式回傳資料。
- DictReader
    - 以字典形式回傳資料。
    - 首列為key  
```python
import csv

# 開啟csv檔
csvfile = open('test.csv', 'r')

# 把檔案存到csv物件
# 進入讀模式
# 以列表形式輸出
csv_obj = csv.reader(csv_obj)
# 以字典形式輸出
csv_obj = csv.DistReader(csv_obj)

rows = [row for row in csv_obj]
print(rows)
# csv_obj.close()
```

### 4. 讀取某行資料
```python
import csv

csvfile = open('test.csv', 'r')
# 以列表形式輸出
csv_obj = csv.reader(csv_obj)
for row in csv_obj:
    if 'Amy' in row:
    	print(row)
# 以dict形式輸出
csv_obj = csv.DictReader(csv_obj)
for row in csv_obj:
    if row['name'] == 'Amy':
    	print(row)
```
### 爬取table資料，並且存到CSV
```python
import csv
from urllib.request import urlopen
from bs4 import BeautifulSoup

html = urlopen("https://zh.wikipedia.org/wiki/%E9%82%B5%E5%A5%95%E7%8E%AB")
bs = BeautifulSoup(html, "html.parser")

# 尋找第1個table
table = bs.find_all("table", class_="wikitable")[1]   # 問題!!!第2個表格就亂掉，好像會自動換行
rows = table.find_all('tr')
# print(table)
# print('*'*20)
# print(table.find_all('tr'))
# print('*'*20)

csvFile = open('米米.csv', 'wt+', encoding="utf-8")  # encoding="utf-8" ??? 不加有錯誤
writer = csv.writer(csvFile)

try:
    for row in rows:
        csvRow = []
        for cell in row.find_all(['th', 'td']):
            csvRow.append(cell.get_text())
        # 將[]裡的資料寫入米米.csv
        writer.writerow(csvRow)
finally:
    csvFile.close()
```
