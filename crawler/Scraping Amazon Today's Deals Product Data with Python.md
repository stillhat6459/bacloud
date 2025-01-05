
# Scraping Amazon Today's Deals Product Data with Python

## Introduction

Scraping data from Amazon's **Today's Deals** page can provide valuable insights into product trends, pricing, and discounts. This article demonstrates how to scrape data from Amazon using **Selenium** and Python. By following the steps outlined, you can automate the process of collecting data and saving it into structured formats like Excel.

---

## Stop Wasting Time on Proxies and CAPTCHAs!

ScraperAPI’s simple API manages proxies and bypasses CAPTCHAs automatically, allowing you to focus on extracting data effortlessly. Start scraping Amazon, Google, Walmart, and more with ease.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Target Website

The target URL for this scraping project is:

```
https://www.amazon.com/gp/goldbox/
```

This URL points to Amazon's **Today's Deals** page. The script navigates through this page, extracts data, and saves it in an Excel file.

![Amazon Today's Deals](https://i-blog.csdnimg.cn/blog_migrate/7a33023d7971f9a272780e96e3188dc1.png)

---

## Prerequisites

Before running the script, ensure the following:

- Install Python 3.x
- Install required Python libraries: `selenium`, `fake_useragent`, `lxml`, `pandas`
- Download the appropriate **ChromeDriver** for Selenium
- Install Google Chrome browser

---

## Python Script: Scraping Amazon Data

Below is the optimized and cleaned version of the Python script for scraping Amazon deals:

```python
# -*- coding: utf-8 -*-
import time
import random
import pandas as pd
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.chrome.options import Options
from fake_useragent import UserAgent
from lxml import etree
from datetime import datetime

class AmazonScraper:
    def __init__(self):
        chrome_options = Options()
        ua = UserAgent()
        prefs = {"profile.managed_default_content_settings.images": 2}
        chrome_options.add_experimental_option("prefs", prefs)
        chrome_options.add_argument(f'user-agent={ua.chrome}')
        self.browser = webdriver.Chrome(options=chrome_options, executable_path="chromedriver.exe")
        self.wait = WebDriverWait(self.browser, 10)

    def set_zip_code(self, zip_code="90017"):
        """Set the ZIP code to access location-specific deals."""
        try:
            self.browser.get("https://www.amazon.com/")
            address_button = self.wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR, '#nav-global-location-slot > span > a')))
            address_button.click()
            time.sleep(random.randint(1, 3))
            input_field = self.wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '#GLUXZipUpdateInput')))
            input_field.send_keys(zip_code)
            set_button = self.wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR, '#GLUXZipUpdate > span > input')))
            set_button.click()
            time.sleep(2)
            print(f"ZIP code set to {zip_code}")
        except Exception as e:
            print(f"Error setting ZIP code: {e}")

    def scrape_page(self, url):
        """Scrape product data from a single page."""
        try:
            self.browser.get(url)
            time.sleep(3)
            html_source = self.browser.execute_script("return document.documentElement.outerHTML")
            html_tree = etree.HTML(html_source)
            products = html_tree.xpath('//*[@id="widgetContent"]/div')
            scraped_data = []

            for i, product in enumerate(products):
                product_text = product.xpath('.//text()')
                product_text = [text.strip() for text in product_text if text.strip()]
                href = product.xpath('.//a[@id="dealImage"]/@href')
                scraped_data.append((href, product_text))
                print(f"Product {i + 1}: {product_text}")

            return scraped_data
        except Exception as e:
            print(f"Error scraping page: {e}")
            return []

    def quit(self):
        """Close the browser."""
        self.browser.quit()

def save_to_excel(data):
    """Save scraped data to an Excel file."""
    file_name = f"Amazon_Deals_{datetime.now().date()}.xlsx"
    df = pd.DataFrame(data, columns=["URL", "Details"])
    df.to_excel(file_name, index=False)
    print(f"Data saved to {file_name}")

if __name__ == '__main__':
    scraper = AmazonScraper()
    scraper.set_zip_code("90017")
    deals_url = "https://www.amazon.com/gp/goldbox/"
    all_data = []

    for page in range(1, 6):  # Scrape the first 5 pages
        print(f"Scraping page {page}...")
        data = scraper.scrape_page(deals_url)
        all_data.extend(data)
        time.sleep(random.randint(2, 4))

    scraper.quit()
    save_to_excel(all_data)
```

---

## Results

After running the script, the scraped data will look like this in Excel:

![Scraped Data](https://i-blog.csdnimg.cn/blog_migrate/26ad29fd0c35e018d5f8522b95926be1.png)

---

## Additional Tips for Scraping Amazon

1. **Avoid Getting Blocked**:
   - Use a rotating proxy service like [ScraperAPI](https://bit.ly/Scraperapi) to avoid IP bans.
   - Randomize user agents and implement delays between requests.

2. **Handle CAPTCHA Challenges**:
   - CAPTCHA challenges may appear on Amazon. Consider using anti-CAPTCHA services for large-scale scraping.

3. **Save Intermediate Data**:
   - Save scraped data periodically to avoid losing progress if the script crashes.

---

## Why Use ScraperAPI?

Scraping Amazon manually can be time-consuming and challenging due to frequent bot detection mechanisms. ScraperAPI simplifies this process by handling proxies and CAPTCHAs automatically.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Conclusion

Scraping Amazon’s Today's Deals page is an excellent way to gather product data for market analysis or personal projects. By combining **Selenium**, **Python**, and additional tools like ScraperAPI, you can efficiently collect and save large amounts of data.

---
