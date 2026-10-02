# ReadBridge Nigeria: Book Catalogue Scraper

A web scraping pipeline that collects book data from [books.toscrape.com](https://books.toscrape.com), cleans it with pandas, and loads it into a PostgreSQL table. Built for the 10Alytics Data Engineering capstone.

## What it does

1. **Extract:** loops through pages 1 to 10 of the site (20 books per page, 200 books total) using `requests` and `BeautifulSoup` with the built-in `html.parser`. It waits 1 second between pages and uses a `safe_get()` function to handle failed requests.
2. **Transform:** cleans the data with pandas.
3. **Load:** writes the result to a PostgreSQL table called `books_catalogue` with SQLAlchemy and then queries it back to confirm the row count.

## The data

| Column | Type | Description |
|---|---|---|
| title | TEXT | Book title |
| price | NUMERIC(10, 2) | Price in GBP, e.g. 51.77 (the "£" symbol is removed) |
| rating | INTEGER | Star rating from 1 to 5 (converted from words like "Three") |
| availability | TEXT | Stock status, e.g. "In stock" |
| page_number | INTEGER | Page of the site the book was scraped from (1 to 10) |
| scraped_at | TIMESTAMP | Date and time the data was scraped |

## Requirements

- Python 3.13.9 (the version this was built and tested with)
- PostgreSQL running locally on port 5432
- A database named `readbridge_db`
- VS Code with the Python and Jupyter extensions (to run the notebook)

## How to run

1. **Clone the repo**
   ```bash
   git clone https://github.com/ByteSpud/readbridge-scraper.git
   cd readbridge-scraper
   ```

2. **Install the libraries**
   ```bash
   pip install requests beautifulsoup4 pandas sqlalchemy psycopg2-binary
   ```
   If `pip` is not recognised on Windows, use `python -m pip install ...` instead.

3. **Create the database** (skip this if it already exists). In pgAdmin or `psql`:
   ```sql
   CREATE DATABASE readbridge_db;
   ```

4. **Open `scraper.ipynb`** in VS Code, click **Select Kernel** (top right) and choose your Python 3.13 interpreter, then click **Run All**.

5. **Enter your password when asked.** When the cell in section 8 runs, VS Code shows a small input box at the top of the window asking for the PostgreSQL password of the `postgres` user. Type it and press Enter. The password is typed in at run time, so it is never stored in the notebook or in GitHub.

Connection settings used by the notebook: host `localhost`, port `5432`, database `readbridge_db`, user `postgres`. If yours are different, change them in section 8 of the notebook.

## Checking the result

After the notebook finishes, run this in `psql` or pgAdmin:

```sql
SELECT COUNT(*) FROM books_catalogue;   -- should return 200
SELECT * FROM books_catalogue LIMIT 5;
```

## Notes

- `if_exists="replace"` is used when loading, so running the notebook again rebuilds the table instead of adding duplicate rows.
- Column types are set explicitly when loading (for example `price` as NUMERIC) so that SQL functions like `ROUND()` work on it.
- The site is a practice site made for scraping and its `robots.txt` allows it.
