
# Web Crawler in C#: Step-by-Step Tutorial [2025]

## Introduction

Web crawling is a powerful technique for systematically discovering and visiting web pages. By building a web crawler in C#, you can efficiently gather data from websites at scale, whether for analytics, monitoring, or other purposes.

In this tutorial, you'll learn how to create a robust web crawler in C# from scratch. We'll cover everything from setting up your environment to implementing advanced features like parallel crawling and handling JavaScript-rendered pages.

---

**Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on extracting data. Get structured data from Amazon, Google, Walmart, and more.**  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## What Is Web Crawling?

Web crawling involves automatically navigating websites by following links to systematically explore their pages. While web scraping focuses on extracting specific data, crawling is about discovering and visiting pages to map a website's structure.

### Crawling vs. Scraping
Think of web crawling as drawing a roadmap of a website, while web scraping collects data from specific locations on that map. These techniques often work hand in hand: a crawler discovers pages, and a scraper extracts the relevant information.

### How Crawlers Work
- **Input**: Start with one or more URLs.
- **Process**: Follow links to discover new pages.
- **Output**: Collect a list of URLs or scrape content from visited pages.

Let’s dive into building your first web crawler with C#!

---

## Step 1: Set Up Your C# Environment

### Initialize a .NET Project
Start by creating a new console project using .NET 8.0. In your terminal, run:
```bash
dotnet new console -n WebCrawler
cd WebCrawler
```

### Install Required Libraries
For crawling and parsing HTML, we'll use the **Html Agility Pack**. Install it via NuGet:
```bash
dotnet add package HtmlAgilityPack
dotnet add package HtmlAgilityPack.CssSelectors.NetCore
```

Your environment is now ready to start building a web crawler in C#.

---

## Step 2: Follow Links on a Website

### Basic Crawler Setup
We’ll begin by visiting a target website and discovering links. For this example, we’ll crawl the [ScrapingCourse e-commerce demo site](https://www.scrapingcourse.com/ecommerce/), which features a paginated list of products.

#### Code Example
Add the following imports to your `Program.cs` file:
```csharp
using HtmlAgilityPack;
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        string baseUrl = "https://www.scrapingcourse.com/ecommerce/";
        Queue<string> pagesToVisit = new Queue<string>();
        HashSet<string> visitedPages = new HashSet<string>();

        pagesToVisit.Enqueue(baseUrl);

        while (pagesToVisit.Count > 0)
        {
            string currentUrl = pagesToVisit.Dequeue();
            if (visitedPages.Contains(currentUrl)) continue;

            visitedPages.Add(currentUrl);

            HtmlWeb web = new HtmlWeb();
            HtmlDocument document = web.Load(currentUrl);

            Console.WriteLine($"Processing: {currentUrl}");

            var links = document.DocumentNode.SelectNodes("//a[@href]");
            if (links != null)
            {
                foreach (var link in links)
                {
                    string href = link.Attributes["href"].Value;
                    if (href.StartsWith("/") || href.StartsWith(baseUrl))
                    {
                        string absoluteUrl = href.StartsWith("/") ? baseUrl + href.Trim('/') : href;
                        if (!visitedPages.Contains(absoluteUrl))
                        {
                            pagesToVisit.Enqueue(absoluteUrl);
                        }
                    }
                }
            }
        }

        Console.WriteLine("Crawling completed!");
    }
}
```

### Explanation
1. **Queue for URLs**: Tracks pages to visit.
2. **HashSet for Visited Pages**: Ensures no page is processed twice.
3. **Html Agility Pack**: Loads pages and extracts `<a>` tags to find links.

### Run the Code
Run the crawler with:
```bash
dotnet run
```
You’ll see output showing the URLs being processed.

---

## Step 3: Extract Data From Pages

Now that our crawler navigates through pages, let's extract product details such as:
- **Name**
- **Price**
- **Image URL**

### Analyze the HTML Structure
Inspect the product listing page in your browser's DevTools. Each product is in an `<li>` element with the class `product`. Within this element:
- Name: `<h2 class="product-name">`
- Price: `<span class="product-price">`
- Image: `<img class="product-image" src="...">`

### Code Example
Define a `Product` class to store extracted data:
```csharp
public class Product
{
    public string Name { get; set; }
    public string Price { get; set; }
    public string ImageUrl { get; set; }
}
```

Modify the crawler to extract product data:
```csharp
List<Product> products = new List<Product>();

while (pagesToVisit.Count > 0)
{
    string currentUrl = pagesToVisit.Dequeue();
    if (visitedPages.Contains(currentUrl)) continue;

    visitedPages.Add(currentUrl);

    HtmlWeb web = new HtmlWeb();
    HtmlDocument document = web.Load(currentUrl);

    Console.WriteLine($"Processing: {currentUrl}");

    // Extract product data
    var productNodes = document.DocumentNode.SelectNodes("//li[contains(@class, 'product')]");
    if (productNodes != null)
    {
        foreach (var node in productNodes)
        {
            string name = node.SelectSingleNode(".//h2[@class='product-name']").InnerText;
            string price = node.SelectSingleNode(".//span[@class='product-price']").InnerText;
            string imageUrl = node.SelectSingleNode(".//img[@class='product-image']").GetAttributeValue("src", "");

            products.Add(new Product { Name = name, Price = price, ImageUrl = imageUrl });
            Console.WriteLine($"Found product: {name}");
        }
    }
}
```

---

## Step 4: Save Data to CSV

To store the extracted data, save it in a CSV file. Add the **CsvHelper** library:
```bash
dotnet add package CsvHelper
```

### Code Example
Add the following to save the data:
```csharp
using CsvHelper;
using System.Globalization;
using System.IO;

using (var writer = new StreamWriter("products.csv"))
using (var csv = new CsvWriter(writer, CultureInfo.InvariantCulture))
{
    csv.WriteRecords(products);
}

Console.WriteLine("Data saved to products.csv");
```

Run the script, and you’ll find a `products.csv` file with the extracted data.

---

## Step 5: Optimize the Crawler

### Avoid Getting Blocked
To prevent blocking, implement these best practices:
1. **Use Proxies**: Rotate IPs to avoid detection.
2. **Delay Requests**: Add random delays between requests using `Thread.Sleep()`.
3. **User-Agent Rotation**: Mimic different browsers with randomized headers.

Alternatively, use **ScraperAPI** to handle these complexities effortlessly:
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Conclusion

You've successfully built a functional web crawler in C#. This tool:
- Crawls through pages
- Extracts structured data
- Saves the data in a CSV file

For advanced crawling, consider implementing:
- **Parallel Crawling**: Use `async/await` for faster processing.
- **JavaScript Rendering**: Use libraries like Selenium or PuppeteerSharp for dynamic websites.

Happy crawling!
