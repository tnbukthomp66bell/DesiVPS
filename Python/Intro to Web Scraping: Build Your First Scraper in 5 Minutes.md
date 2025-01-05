
# Intro to Web Scraping: Build Your First Scraper in 5 Minutes

## Introduction

Web scraping is an exciting and rewarding area of coding. With nearly infinite data on the internet, scraping opens up endless opportunities to collect and analyze information for various purposes. Whether you're tracking trends, extracting product details, or gathering market data, scraping offers a powerful way to automate these tasks.

When I started, I had no coding experience. However, within 20 minutes of Googling and experimenting, I built my first scraper to **track mortgage interest rates** in real time. Now, I want to help you build your first scraper in just five minutes.

---

**Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on extracting data. Get structured data from Amazon, Google, Walmart, and more.**  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

In this guide, I'll show you how to build a simple scraper using Python or JavaScript. Both are excellent options—it all comes down to personal preference.

### Prerequisites:
- A code editor (like VS Code)
- Basic familiarity with programming concepts

That's it! Let's dive in.

---

## Step 1: Set Up Your Project

1. **Create a Folder and File**  
   Create a new folder on your computer and a file inside it. Name your file:
   - For Python: `scraper-python.py`
   - For JavaScript: `scraper-javascript.js`

2. **Install Required Libraries**  
   Open your terminal and install the necessary libraries for your chosen language:  
   - For Python:
     ```bash
     pip install requests
     pip install beautifulsoup4
     ```
   - For JavaScript:
     ```bash
     npm init -y
     npm install cheerio
     ```

3. **Open the Folder in Your IDE**  
   Open the folder in your preferred Integrated Development Environment (IDE), such as Visual Studio Code.

---

## Step 2: Make a Network Request

Now, let's make a simple GET request to a website and retrieve its HTML. For this example, we'll scrape **example.com**, a simple website with no bot detection or authentication.

### Code Example:

- **Python**:
  ```python
  import requests

  url = "https://example.com/"
  response = requests.get(url)
  print(response.text)
  ```

- **JavaScript**:
  ```javascript
  const axios = require('axios');

  const url = "https://example.com/";
  axios.get(url).then(response => {
    console.log(response.data);
  });
  ```

### Run Your Script:

- For Python:  
  ```bash
  python scraper-python.py
  ```
- For JavaScript:  
  ```bash
  node scraper-javascript.js
  ```

You should see the HTML content of the website printed in your terminal.

---

## Step 3: Parse the HTML

To extract specific elements from the HTML, we'll use libraries designed for navigating and parsing web pages:
- **Python**: `BeautifulSoup`
- **JavaScript**: `cheerio`

For this example, we'll extract three items from the HTML of **example.com**:
1. The page title
2. The main text
3. The "More information..." link

### Code Example:

- **Python**:
  ```python
  from bs4 import BeautifulSoup
  import requests

  url = "https://example.com/"
  response = requests.get(url)
  soup = BeautifulSoup(response.text, "html.parser")

  title = soup.title.text
  text = soup.find("p").text
  link = soup.find("a")["href"]

  print(f"Title: {title}")
  print(f"Text: {text}")
  print(f"Link: {link}")
  ```

- **JavaScript**:
  ```javascript
  const axios = require('axios');
  const cheerio = require('cheerio');

  const url = "https://example.com/";
  axios.get(url).then(response => {
    const $ = cheerio.load(response.data);

    const title = $("title").text();
    const text = $("p").first().text();
    const link = $("a").attr("href");

    console.log(`Title: ${title}`);
    console.log(`Text: ${text}`);
    console.log(`Link: ${link}`);
  });
  ```

### Expected Output:

When you run your script, the console should display something like this:
```
Title: Example Domain
Text: This domain is for use in illustrative examples in documents. You may use this domain in literature without prior coordination or asking for permission.
Link: https://www.iana.org/domains/example
```

---

## Step 4: Add Advanced Features (Optional)

Once you've mastered the basics, you can enhance your scraper by:
- **Handling Pagination**: Scrape multiple pages of data.
- **Adding Proxies**: Avoid IP bans by rotating proxies.
- **Handling Captchas**: Use tools like ScraperAPI for CAPTCHA-solving and IP rotation.

---

## Key Takeaways

- **Start Simple**: Choose a basic website like example.com for your first scraper.  
- **Master Libraries**: Learn `BeautifulSoup` (Python) or `cheerio` (JavaScript) for efficient HTML parsing.  
- **Use Proxies for Scale**: As you scrape larger sites, consider services like ScraperAPI to handle proxies and CAPTCHAs effortlessly.

---

**Ready to scrape smarter, not harder?**  
ScraperAPI simplifies web scraping with powerful features like IP rotation, CAPTCHA solving, and data structuring.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)
