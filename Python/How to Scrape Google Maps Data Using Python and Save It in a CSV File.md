
# How to Scrape Google Maps Data Using Python and Save It in a CSV File

Google Maps is a goldmine of location-based data, offering details like phone numbers, reviews, ratings, operating hours, and more. Scraping this data can help businesses generate leads, perform competitor analysis, or enhance their databases with real-time information. In this guide, we’ll show you how to efficiently scrape Google Maps data using Python and store the results in a CSV file.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Why Scrape Google Maps Data?

Here are some common use cases for scraped Google Maps data:

- **Lead Generation**: Extract phone numbers and contact details to build a list of potential clients.
- **Sentiment Analysis**: Analyze reviews to understand customer opinions about a business or location.
- **Competitor Analysis**: Compare reviews, ratings, and other key metrics with your competitors.
- **Real-Time Database Updates**: Keep your business information up-to-date with real-time data.
- **Geospatial Mapping**: Map locations for industries like real estate or logistics.

---

## Setting Up the Environment

### Step 1: Install Python and Create a Project Folder

First, ensure Python is installed on your machine. If not, download it [here](https://www.python.org/downloads/).

Create a folder to store your Python script. For example:

```bash
mkdir maps
```

### Step 2: Install Required Libraries

Inside your project folder, install the following Python libraries:

- **Requests**: For making HTTP requests to the Google Maps API.
- **Pandas**: For storing and processing scraped data in a CSV format.

Run the following commands in your terminal:

```bash
pip install requests
pip install pandas
```

---

## Signing Up for an API

To scrape Google Maps data, you’ll need an API key. Sign up for the [ScraperAPI trial pack](https://bit.ly/Scraperapi), which includes **1000 free credits** for testing.

With this key, you can query the Google Maps API for details such as business names, addresses, and phone numbers.

---

## Writing the Python Script

Let’s say you’re a hardware manufacturer based in New York, and you want to find hardware shops nearby. Here’s how you can extract their contact details using the Google Maps API.

### Step 1: Define the API Parameters

The Google Maps API requires:
- **Coordinates**: Latitude and longitude of the target location.
- **Query**: Search term, such as "hardware shops near me."

### Step 2: Write the Python Script

Create a Python file in the `maps` folder and name it `leads.py`. Copy the following code into the file:

```python
import requests
import pandas as pd

# Define your API key
api_key = "your-api-key"
url = "https://api.scrapingdog.com/google_maps"
results = []

# Define the search parameters
params = {
    "api_key": api_key,
    "query": "hardware shops near me",
    "ll": "@40.6974881,-73.979681,10z",  # New York coordinates
    "page": 0
}

# Send the API request
response = requests.get(url, params=params)

# Check if the request was successful
if response.status_code == 200:
    data = response.json()
    
    # Extract the relevant data
    for result in data['search_results']:
        results.append({
            "Title": result.get("title"),
            "Address": result.get("address"),
            "PhoneNumber": result.get("phone")
        })
    
    # Save the data to a CSV file
    df = pd.DataFrame(results)
    df.to_csv('leads.csv', index=False, encoding='utf-8')
    print("Data saved to leads.csv")
else:
    print(f"Request failed with status code: {response.status_code}")
```

---

## How the Script Works

1. **API Query**: The script sends a GET request to the API, including the search query and coordinates.
2. **Data Extraction**: The JSON response is parsed to extract the business name, address, and phone number.
3. **Data Storage**: The extracted data is stored in a Pandas DataFrame and exported to a CSV file (`leads.csv`).

---

## Running the Script

Run the script in your terminal:

```bash
python leads.py
```

Once executed, a `leads.csv` file will be created in the `maps` folder. It will contain all the extracted data, including business names, addresses, and phone numbers.

---

## Advanced Use Case: Extracting Google Reviews

To analyze customer feedback, you can scrape reviews using the Google Reviews API. Here’s a quick overview:

### Finding the Place ID

Each business on Google Maps has a unique Place ID (e.g., `0x89c258e4c2066753:0x64f50a662bfb26d7`). You can find this ID by searching for the business on Google Maps and extracting it from the URL.

For example:
```
https://www.google.com/maps/place/le+cafe+coffee/@40.7320513,-74.0106458,13z/data=!4m10!...
```

### Using the Reviews API

Here’s a sample Python script to scrape reviews:

```python
import requests
import pandas as pd

api_key = "your-api-key"
url = "https://api.scrapingdog.com/google_maps_reviews"

# Define the parameters
params = {
    "api_key": api_key,
    "data_id": "0x89c258e4c2066753:0x64f50a662bfb26d7",  # Place ID
    "page": 0
}

# Send the API request
response = requests.get(url, params=params)

# Check if the request was successful
if response.status_code == 200:
    reviews = response.json()
    results = []

    for review in reviews['data']:
        results.append({
            "Reviewer": review.get("name"),
            "Rating": review.get("rating"),
            "Review": review.get("text")
        })
    
    # Save to CSV
    df = pd.DataFrame(results)
    df.to_csv('reviews.csv', index=False, encoding='utf-8')
    print("Reviews saved to reviews.csv")
else:
    print(f"Request failed with status code: {response.status_code}")
```

---

## Conclusion

Using Python to scrape Google Maps data is an excellent way to automate lead generation and competitor analysis. By leveraging tools like ScraperAPI, you can efficiently handle challenges like proxies and CAPTCHAs while scaling your data collection efforts.

Get started with ScraperAPI today and take the hassle out of web scraping! 👉 [Start your free trial now!](https://bit.ly/Scraperapi)
