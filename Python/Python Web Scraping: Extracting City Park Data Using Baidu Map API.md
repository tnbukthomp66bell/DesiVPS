
# Python Web Scraping: Extracting City Park Data Using Baidu Map API

This tutorial demonstrates how to use the Baidu Map API to scrape data about city parks and store the results in a MySQL database. By leveraging Python, we can automate the process of retrieving general and detailed park information and organize it for further analysis.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Overview of the Process

1. Use the **Baidu Map API** to retrieve park information for specified cities.
2. Save general park data (via the first API) into a `city` table.
3. Use the park `uid` (unique identifier) from the `city` table to fetch detailed park data using the second API.
4. Store the detailed information in a `park` table.

### Required APIs

1. **Search API**  
   URL: `http://api.map.baidu.com/place/v2/search`  
   - Retrieves general park information.  

2. **Detail API**  
   URL: `http://api.map.baidu.com/place/v2/detail`  
   - Retrieves detailed information for specific parks based on `uid`.

---

## Step 1: Fetch Data Using the Search API

The first API provides general park information, including the park name, location, and address. Below is the Python implementation:

### Data File
We start with a `cities.txt` file containing city names and park counts.

### Code for Fetching and Storing Data

```python
import requests
import json
import MySQLdb
from datetime import datetime

# Read cities from txt file
city_list = []
with open('cities.txt', 'r', encoding='utf-8') as f:
    for line in f:
        if line.strip():
            city = line.split('\t')[0]
            city_list.append(city)

# Function to fetch park data
def getjson(city, page_num=0):
    url = 'http://api.map.baidu.com/place/v2/search'
    params = {
        'q': '公园',
        'region': city,
        'scope': '2',
        'page_size': '20',
        'page_num': page_num,
        'output': 'json',
        'ak': 'XM53LMurtNQaAPFuKVy1WzSyZCNmNA9H',
    }
    headers = {'User-Agent': 'Mozilla/5.0'}
    response = requests.get(url, params=params, headers=headers)
    return response.json()

# Connect to MySQL database
conn = MySQLdb.connect(host='localhost', user='root', password='root', db='baidumap', charset='utf8')
cur = conn.cursor()

# Fetch and store data in MySQL
for city in city_list:
    page_num = 0
    not_last_page = True
    while not_last_page:
        data = getjson(city, page_num)
        if data.get('results'):
            for result in data['results']:
                sql = """INSERT INTO baidumap.city 
(city, park, location_lat, location_lng, address, street_id, uid, time)
VALUES (%s, %s, %s, %s, %s, %s, %s, %s);"""
                cur.execute(sql, (
                    city,
                    result.get('name'),
                    result.get('location', {}).get('lat'),
                    result.get('location', {}).get('lng'),
                    result.get('address'),
                    result.get('street_id'),
                    result.get('uid'),
                    datetime.now()
                ))
                conn.commit()
            page_num += 1
        else:
            not_last_page = False

cur.close()
conn.close()
```

### MySQL Table Structure: `city`

The `city` table stores the following fields:

| Field         | Type      | Description               |
|---------------|-----------|---------------------------|
| `id`          | INT       | Auto-increment primary key|
| `city`        | VARCHAR   | City name                 |
| `park`        | VARCHAR   | Park name                 |
| `location_lat`| FLOAT     | Latitude                  |
| `location_lng`| FLOAT     | Longitude                 |
| `address`     | VARCHAR   | Park address              |
| `street_id`   | VARCHAR   | Street ID                 |
| `uid`         | VARCHAR   | Unique park ID            |
| `time`        | DATETIME  | Timestamp of the record   |

---

## Step 2: Fetch Detailed Park Information Using the Detail API

Using the `uid` from the `city` table, we query the second API to retrieve detailed park data such as ratings, comments, and tags.

### Code for Fetching Detailed Data

```python
# Function to fetch detailed data
def getjson(uid):
    url = 'http://api.map.baidu.com/place/v2/detail'
    params = {
        'uid': uid,
        'scope': '2',
        'output': 'json',
        'ak': 'XM53LMurtNQaAPFuKVy1WzSyZCNmNA9H',
    }
    headers = {'User-Agent': 'Mozilla/5.0'}
    response = requests.get(url, params=params, headers=headers)
    return response.json()

# Connect to MySQL
conn = MySQLdb.connect(host='localhost', user='root', password='root', db='baidumap', charset='utf8')
cur = conn.cursor()

# Get uids from city table
cur.execute("SELECT uid FROM baidumap.city WHERE id > 0;")
uids = cur.fetchall()

# Fetch and store detailed park data
for uid in uids:
    uid = uid[0]
    data = getjson(uid).get('result', {})
    if data:
        sql = """INSERT INTO baidumap.park 
(park, location_lat, location_lng, address, street_id, telephone, detail, uid, 
tag, detail_url, type, overall_rating, image_num, comment_num, shop_hours, alias, 
scope_type, scope_grade, description, time) 
VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s);"""
        cur.execute(sql, (
            data.get('name'),
            data.get('location', {}).get('lat'),
            data.get('location', {}).get('lng'),
            data.get('address'),
            data.get('street_id'),
            data.get('telephone'),
            data.get('detail'),
            uid,
            data.get('detail_info', {}).get('tag'),
            data.get('detail_info', {}).get('detail_url'),
            data.get('detail_info', {}).get('type'),
            data.get('detail_info', {}).get('overall_rating'),
            data.get('detail_info', {}).get('image_num'),
            data.get('detail_info', {}).get('comment_num'),
            data.get('detail_info', {}).get('shop_hours'),
            data.get('detail_info', {}).get('alias'),
            data.get('detail_info', {}).get('scope_type'),
            data.get('detail_info', {}).get('scope_grade'),
            data.get('detail_info', {}).get('description'),
            datetime.now()
        ))
        conn.commit()

cur.close()
conn.close()
```

### MySQL Table Structure: `park`

The `park` table stores detailed park data:

| Field            | Type      | Description                  |
|------------------|-----------|------------------------------|
| `id`             | INT       | Auto-increment primary key   |
| `park`           | VARCHAR   | Park name                    |
| `location_lat`   | FLOAT     | Latitude                     |
| `location_lng`   | FLOAT     | Longitude                    |
| `address`        | VARCHAR   | Address                      |
| `street_id`      | VARCHAR   | Street ID                    |
| `telephone`      | VARCHAR   | Telephone number             |
| `detail`         | TEXT      | Detailed information         |
| `tag`            | VARCHAR   | Park tags                    |
| `detail_url`     | VARCHAR   | Detail page URL              |
| `type`           | VARCHAR   | Type of park                 |
| `overall_rating` | FLOAT     | Overall rating               |
| `image_num`      | INT       | Number of images             |
| `comment_num`    | INT       | Number of comments           |
| `shop_hours`     | TEXT      | Shop hours                   |
| `description`    | TEXT      | Park description             |
| `time`           | DATETIME  | Timestamp                    |

---

## Conclusion

By combining Python with the Baidu Map API, we can efficiently scrape and manage large datasets for city parks. This process ensures that data is structured, accurate, and ready for analysis. Integrating tools like ScraperAPI can further streamline such scraping processes by handling proxies and CAPTCHAs.

Start automating your data collection today and take control of your data pipeline!
