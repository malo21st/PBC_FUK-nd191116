# Python Boot Camp in 福岡2nd

ＴＡ（講師のアシスタント）をする際に、テキストを勉強した時の副産物などです。

## 2.2.FizzBuzz [text](http://pycamp.pycon.jp/textbook/2_intro.html#fizzbuzz)

 - リスト内包表記を使って実装
 - [リスト内包表記でFizzBuzz](https://qiita.com/ssh0/items/23629be59af1dd3e7c41)
    
```python
print(*[(i%3==0)*'Fizz' + (i%5==0)*'Buzz' or i for i in range(1, 101)])
```

 - 辞書を使って実装（私の作品）
    
```python
surplus15 = {0:"FizzBuzz", 3:"Fizz", 5:"Buzz", 6:"Fizz", 9:"Fizz", 10:"Buzz", 12:"Fizz"}

for i in range(1,101):
    print(surplus15.get(i % 15, i), end=" ")
```

## 7.3. シンプルなWeb APIのコード [text](http://pycamp.pycon.jp/textbook/7_scraping.html#id3)

 - 開催地の緯度・経度を取得して、地図上に表示
 - [folium](https://python-visualization.github.io/folium/)：地図表示のleaflet.jsをPythonで扱えるライブラリ
    
```python
# events.py 改造
import requests
import folium # pip install folium
from folium import plugins

Tokyo_St = [35.681236, 139.767125] # 地図の中心を東京駅（特に、意味なし）

def get_event_loc():
    params = {
        'keyword': 'python',
        'ym': '201911',
        'count': 100, # デフォルトは10　最大は100まで      
    }

    url = 'https://connpass.com/api/v1/event/'
    r = requests.get(url, params=params)
    event_info = r.json()  # レスポンスのJSONを変換

    loc = []
    print('データ件数:', event_info['results_returned'], end="\n")  # 件数を表示
    for event in event_info['events']:
        if event['lat']:
            loc.append([float(event['lat']), float(event['lon'])])
    print('場所データ:', len(loc), end="\n")  # 件数を表示
    return loc


event_loc = get_event_loc()
event_map = folium.Map(Tokyo_St, zoom_start=4)
plugins.MarkerCluster(event_loc).add_to(event_map)

event_map.save("event_map.html")
#event_map # Jupyter Notebook は、こちらを使用ください。
```

## 7.5. 作り変えてみよう [text](http://pycamp.pycon.jp/textbook/7_scraping.html#id7)

 - ウェブスクレピングは、XPathがおススメ。
 - [ChromeでXPathを簡単に取得する](https://www.hacky.xyz/entry/2017/04/15/164049)
 - （参考書籍）[Pythonクローリング&スクレイピング -データ収集・解析のための実践開発ガイド](https://www.amazon.co.jp/gp/product/4774183679/)

```python
# https://www.python.org/blogs/ から Latest News のお題を表示する
import requests
import lxml.html # pip install lxml

response = requests.get('https://www.python.org/blogs/')
html = lxml.html.fromstring(response.content)

for i in range(1, 100):
    try:
        xpath = '//*[@id="content"]/div/section/div/div[1]/div/ul/li[' + str(i) + ']/h3/a'
        title = html.xpath(xpath)[0].text
    except:
        break
    print('{} : {}'.format(i, title))
```
