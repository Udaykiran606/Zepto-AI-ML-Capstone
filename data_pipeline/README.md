# Module 1 — Data Pipeline (`/data_pipeline`)

## What This Project Does
This project automatically collects book data from [books.toscrape.com](http://books.toscrape.com/), cleans up messy text, converts prices from GBP to INR, saves everything into a database, and tests it with SQL queries.

---

## Steps Explained Simply

1. **Scraping Data:** 
   - Uses `requests` and `BeautifulSoup` to grab titles, prices, ratings, and stock status across 8 categories (collects over 60 books).
2. **Cleaning & Fixing Missing Values:**
   - Removes currency symbols like `£`.
   - Replaces missing numbers using the **median** (middle value).
   - Converts ratings from words ("Three") to numbers (`3`).
3. **Currency Conversion:**
   - Multiplies prices in GBP by `105.50` to calculate INR:
     $$1 \text{ GBP} = 105.50 \text{ INR}$$
4. **Database Storage:**
   - Saves everything into an SQLite database (`catalog_pipeline.db`) split into two linked tables: `categories` and `books`.
5. **Testing & Queries:**
   - Runs SQL queries (`SELECT`, `WHERE`, `ORDER BY`, `JOIN`) and verifies that Pandas gives the exact same result.

---

## How to Run
1. Install requirements:
   ```bash
   pip install requests beautifulsoup4 pandas numpy