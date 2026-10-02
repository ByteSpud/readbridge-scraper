# ReadBridge Nigeria: Book Catalogue Scraper

A web scraping pipeline that collects book data from [books.toscrape.com](https://books.toscrape.com), cleans it with pandas, and loads it into a PostgreSQL table. Built for the 10Alytics Data Engineering capstone.

## What it does

1. **Extract:** loops through pages 1 to 10 of the site (20 books per page, 200 books total) using `requests` and `BeautifulSoup`. It waits 1 second between pages and uses a `safe_get()` function to handle failed requests.
2. **Transform:** cleans the data with pandas.
3. **Load:** writes the result to a PostgreSQL table called `books_catalogue` with SQLAlchemy and then queries it back to confirm the row count.

## The data

| Column | Type | Description |
|---|---|---|
| title | TEXT | Book title |
| price | NUMERIC (float) | Price in GBP, e.g. 51.77 (the "£" symbol is removed) |
| rating | INTEGER | Star rating from 1 to 5 (converted from words like "Three") |
| availability | TEXT | Stock status, e.g. "In stock" |
| page_number | INTEGER | Page of the site the book was scraped from (1 to 10) |
| scraped_at | TIMESTAMP | Date and time the row was scraped |

## Requirements

- Python 3.9 or newer
- PostgreSQL running locally
- A database named `readbridge_db`

## How to run

1. **Clone the repo**
   ```bash
   git clone https://github.com/ByteSpud/readbridge-scraper.git
   cd readbridge-scraper
   ```

2. **Install the libraries**
   ```bash
   pip install -r requirements.txt
   ```

3. **Create the database** (skip this if it already exists). In pgAdmin or `psql`:
   ```sql
   CREATE DATABASE readbridge_db;
   ```

4. **Open `scraper.ipynb`** in VS Code, pick your Python kernel, and choose **Run All**.

5. When the cell in section 8 runs, a box will ask for your PostgreSQL password for the `postgres` user. The password is typed in at run time so it is never stored in the notebook or in GitHub.

Connection settings used by the notebook: host `localhost`, port `5432`, database `readbridge_db`, user `postgres`. If yours are different, change them in section 8 of the notebook.

## Checking the result

After the notebook finishes, run this in `psql` or pgAdmin:

```sql
SELECT COUNT(*) FROM books_catalogue;   -- should return 200
SELECT * FROM books_catalogue LIMIT 5;
```

## Notes

- `if_exists="replace"` is used when loading, so running the notebook again rebuilds the table instead of adding duplicate rows.
- The site is a practice site made for scraping and its `robots.txt` allows it.
