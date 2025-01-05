
# How to Scrape Amazon Product Data Using TypeScript, Cheerio, and Crawlee

Web scraping Amazon can be challenging due to its vast and complex website structure. However, with the right tools like **TypeScript**, **Cheerio**, and **Crawlee**, you can streamline this process. This guide will show you how to extract Amazon product data efficiently, including product titles, prices, images, and more, while addressing challenges like blocking and CAPTCHAs.

---

## Stop Wasting Time on Proxies and CAPTCHAs!

ScraperAPI handles millions of web scraping requests seamlessly, allowing you to focus on your data without worrying about technical challenges like proxies or CAPTCHAs.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Prerequisites

Before diving in, ensure you have:
- A basic understanding of **TypeScript**.
- Familiarity with **HTML structure** and tools like browser DevTools.
- Some knowledge of **Cheerio** and **Crawlee** (optional, but helpful).

**Crawlee** is an open-source library with over 12,000 stars on GitHub. You can explore its [source code here](https://github.com/apify/crawlee) and experiment with its built-in templates.

---

## Setting Up the Scraper

### Fields to Scrape
Before coding, identify the product fields you want to extract:
- **Title**: The product name.
- **Price**: Current and list prices.
- **Review Rating**: Average rating.
- **Review Count**: Number of customer reviews.
- **Image URLs**: Links to product images.
- **Attributes**: Details like brand, color, size, etc.

Inspect these fields using browser DevTools to find their **CSS selectors**.

---

### Writing the Scraper

#### Example: Extracting Product Titles
1. Use DevTools (Ctrl + Shift + C in Chrome) to inspect the product title selector.
2. For Amazon, the selector is typically `span#productTitle`.

Here’s a basic scraping function:

```typescript
import * as cheerio from 'cheerio';

function extractProductDetails(html: string) {
    const $ = cheerio.load(html);
    const title = $('span#productTitle').text().trim();
    return { title };
}
```

This principle applies to other fields like prices, images, and reviews.

---

## Improving the Scraper

### Parsing Numeric Fields
Prices and review counts are often strings. Convert them to numbers using utility functions:

```typescript
function parseNumberValue(value: string): number | null {
    const numericValue = parseFloat(value.replace(/[^0-9.]/g, ''));
    return isNaN(numericValue) ? null : numericValue;
}
```

Update your scraper to handle these fields:

```typescript
function extractProductDetails(html: string) {
    const $ = cheerio.load(html);
    const price = parseNumberValue($('#priceblock_ourprice').text());
    const reviewCount = parseNumberValue($('#acrCustomerReviewText').text());
    return { price, reviewCount };
}
```

---

## Scraping Advanced Fields

### Extracting Image URLs
To scrape multiple images, loop through all matching elements:

```typescript
function extractImages($: cheerio.CheerioAPI): string[] {
    return $('img[data-image-url]').map((_, el) => $(el).attr('data-image-url')).get();
}
```

### Parsing Attributes
Extract product attributes using the `map` function:

```typescript
function extractAttributes($: cheerio.CheerioAPI): { label: string; value: string }[] {
    return $('#productDetails_techSpec_section_1 tr').map((_, el) => ({
        label: $(el).find('th').text().trim(),
        value: $(el).find('td').text().trim(),
    })).get();
}
```

Integrate these functions into your main scraper.

---

## Crawling Amazon Pages with Crawlee

Crawlee simplifies web scraping by managing:
- **Request queues**
- **Automatic scaling**
- **JSON file outputs**

Here’s how to integrate Crawlee with your scraper:

```typescript
import { CheerioCrawler } from 'crawlee';

const crawler = new CheerioCrawler({
    requestHandler: async ({ request, $ }) => {
        const productDetails = extractProductDetails($.html());
        console.log(productDetails);
    },
});

await crawler.run(['https://www.amazon.com/product-link']);
```

This setup crawls product pages and extracts structured data.

---

## Avoiding Blocks When Scraping Amazon

### Common Issues
Amazon employs anti-scraping measures like:
- **CAPTCHAs**
- **IP blocking**

Use these techniques to mitigate issues:

1. **Retry Requests on Blocks**:
   Detect CAPTCHAs and retry requests automatically.

2. **Use Proxies**:
   Rotate IP addresses to avoid detection. Crawlee supports [proxy management](https://crawlee.dev/docs/guides/proxy-management).

3. **Leverage Headless Browsers**:
   Tools like [Playwright](https://crawlee.dev/docs/examples/playwright-crawler) handle dynamic JavaScript content better.

Example of integrating Playwright with Crawlee:

```typescript
import { PlaywrightCrawler } from 'crawlee';

const crawler = new PlaywrightCrawler({
    requestHandler: async ({ page }) => {
        const html = await page.content();
        const $ = cheerio.load(html);
        const productDetails = extractProductDetails($.html());
        console.log(productDetails);
    },
});

await crawler.run(['https://www.amazon.com/product-link']);
```

---

## Example Output

Here’s an example of the extracted data:

```json
{
    "title": "ASUS ROG Strix G16 Gaming Laptop",
    "price": 1799.99,
    "listPrice": 1999.99,
    "reviewRating": 4.3,
    "reviewCount": 372,
    "imageUrls": [
        "https://m.media-amazon.com/images/I/41EWnXeuMzL._AC_US40_.jpg",
        "https://m.media-amazon.com/images/I/51gAOHZbtUL._AC_US40_.jpg"
    ],
    "attributes": [
        { "label": "Brand", "value": "ASUS" },
        { "label": "Screen Size", "value": "16 Inches" },
        { "label": "Color", "value": "Eclipse Gray" }
    ]
}
```

---

## Conclusion

Scraping Amazon with TypeScript, Cheerio, and Crawlee can seem daunting, but with the right approach, it’s manageable. By combining scraping and crawling techniques, you can efficiently extract valuable product data. Use proxies and advanced tools like Playwright for large-scale operations to avoid blocks.

Ready to start your web scraping journey?  
👉 [Start your free trial with ScraperAPI today!](https://bit.ly/Scraperapi)
