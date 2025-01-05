
# Faster Web Scraping with Python and asyncio

## Introduction

Are you still sending sequential web requests while using Jupyter? It's time to level up! This article is designed for intermediate Python programmers to explore how to speed up web scraping using Python's `asyncio`. If you're tired of slow, sequential requests, you're in the right place.

Before diving in, I want to highlight two fantastic books that helped shape this journey:

> **Python Concurrency with asyncio** by Matthew Fowler  
> **Using Asyncio in Python** by Caleb Hattingh  

If you're serious about asynchronous programming, grab these books!

---

**Stop wasting time on proxies and CAPTCHAs! ScraperAPI’s simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more.**  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Getting Some Data to Work With

We'll use a sample file from Wikimedia dumps. Since it's a `.gzip` file, we need to decompress it using Python's `gzip` module, which returns a byte string that can be decoded to UTF-8.

### Observations About the Data

The data contains the following:

- A large string with a **header row** indicating two columns: `page_namespace` and `page_title`.
- It's **tab-delimited**, denoted by `\t` characters.
- Rows are separated by **new line characters** (`\n`).

For simplicity, we'll extract the first 100,000 article titles. Here's a sample:

```python
titles_sample[:10]
```

```plaintext
['!',
 '!!',
 '!!!',
 '!!!!!!!',
 '!!!Fuck_You!!!',
 '!!!Fuck_You!!!_And_Then_Some',
 '!!!Fuck_You!!!_and_Then_Some',
 '!!!_(!!!_album)',
 '!!!_(American_band)',
 '!!!_(Chk_Chk_Chk)']
```

Total number of article titles: **100,000**.

---

## Making the Requests

### Constructing URLs

To scrape Wikipedia, we construct URLs like this:

```python
title = titles_sample[0]
url = WIKIPEDIA_BASE_URL.format(TITLE=title)
print(url)
```

```plaintext
https://en.wikipedia.org/wiki/!
```

### The Sequential Way

Using Python's `requests` library, we can make HTTP GET requests sequentially. However, this approach is painfully slow:

```python
%%timeit -n 1 -r 5
sequential_requests(urls[:10])
```

Output:

```plaintext
4.43 s ± 76.7 ms per loop (mean ± std. dev. of 5 runs, 1 loop each)
```

### The Async Way

Using `asyncio` and `aiohttp`, we can speed up requests by leveraging asynchronous programming. Here's an example:

#### Creating a Timing Decorator

We'll create an `@async_timer` decorator to measure execution time across multiple iterations.

#### Writing Coroutines

Coroutines are functions with the superpower to **pause execution** using `await`, allowing other tasks to run concurrently.

Key steps:

1. **Create a Coroutine Task**: Use `asyncio.create_task()` to wrap coroutines as tasks for execution.
2. **Wait for Tasks**: Use `asyncio.gather()` to run all tasks and return results.

Example of making async requests:

```python
results = await make_requests(urls[:10])
```

Output:

```plaintext
Average time over 5 iterations: 0.81 seconds
```

For a larger batch of requests, the improvement is significant:

```plaintext
Average time over 5 iterations: 42.51 seconds
```

This approach achieves roughly **60 requests per second**.

---

## Handling Exceptions in Asyncio

### Cancelling Pending Tasks on the First Exception

To handle errors gracefully, we can use `asyncio.wait()` to cancel remaining tasks after encountering an exception. For example:

1. Use `asyncio.wait()` with `return_when=asyncio.FIRST_EXCEPTION`.
2. Separate completed tasks into `done` and `pending` sets.
3. Cancel all pending tasks to free up resources.

Output:

```plaintext
number of done tasks: 1
task result: task timed out!
number of pending tasks to cancel: 29
```

---

## Optimizing Task Creation for Large Requests

Creating all tasks upfront can be resource-intensive. Instead, we use a **queue system** with async producers and workers.

Steps:

1. Use `asyncio.Queue` to manage tasks dynamically.
2. Limit the queue size to avoid creating too many tasks upfront.
3. Create async producer and worker functions to add and process tasks.

Output:

```plaintext
producer enqueued https://en.wikipedia.org/wiki/!
worker_0 got https://en.wikipedia.org/wiki/! from queue
worker_1 got https://en.wikipedia.org/wiki/!! from queue
producer exhausted
worker_0 completed task
worker_1 completed task
```

---

## Retrying Failed Requests

To handle retries, create a `retry` coroutine:

1. Specify **max retries**, **timeout**, and **retry intervals**.
2. Raise a custom `TooManyRetries` exception after exceeding retries.
3. Collect successful and failed requests for further analysis.

Example:

```python
results, failed = await make_requests(urls[:10], timeout_seconds=0.5)
```

Output:

```plaintext
request to https://en.wikipedia.org/wiki/! failed. (tried 2 times)
TooManyRetries: https://en.wikipedia.org/wiki/!
```

---

## Conclusion

Async programming with `asyncio` and `aiohttp` dramatically speeds up web scraping, allowing for faster, more efficient data collection. By implementing exception handling, dynamic task creation, and retry logic, you can build robust and scalable web scrapers.

---

**Stop wasting time on proxies and CAPTCHAs! ScraperAPI simplifies millions of web scraping requests, so you can focus on extracting data efficiently.**  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

Happy scraping!  
Justin
