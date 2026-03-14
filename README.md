# 🎵 SQL Music Store Analysis

## 📌 Project Overview
Analysed a normalised music store database in PostgreSQL to answer 
business questions about customer spending, genre popularity, and 
artist revenue — working across 11 interconnected tables.

Focused on three core business problems that required progressively 
complex SQL — from identifying the best-selling artist and their 
top customer, to finding the most popular genre per country, to 
ranking the highest-spending customer in every market.

---

## 🛠 Database & Tools Used
| Component | Description |
|----------|-------------|
| **Database** | PostgreSQL (PostgreSQL RDBMS) |
| **Interface / Client** | pgAdmin4 |
| **Dataset** | Music Store database (ER diagram below) |

---

## 🗂 Database Schema

### Music Store ER Diagram  
![MusicDatabaseSchema](https://user-images.githubusercontent.com/112153548/213707717-bfc9f479-52d9-407b-99e1-e94db7ae10a3.png)

---

## 🧱 Database Structure

The database contains multiple interconnected tables. Below is a description of the key tables and relationships:

| Table | Description |
|--------|-------------|
| **Artist** | Contains artist info such as `ArtistId`, `Name`. |
| **Album** | Holds album details, linked to `ArtistId`. |
| **Track** | Represents individual music tracks. Columns include `TrackId`, `Name`, `AlbumId`, `MediaTypeId`, `GenreId`, `Composer`, `Milliseconds`, `Bytes`, `UnitPrice`. |
| **MediaType** | Lists available media formats (`MediaTypeId`, `Name`). |
| **Genre** | Genre information such as Rock, Pop, Jazz (`GenreId`, `Name`). |
| **Playlist** | Contains playlist information (`PlaylistId`, `Name`). |
| **PlaylistTrack** | Junction table linking playlists and tracks (`PlaylistId`, `TrackId`). |
| **Customer** | Customer details like name, city, country. |
| **Invoice** | Customer purchase transactions. |
| **InvoiceLine** | Individual line item within an invoice (track purchase). |
| **Employee** | Store employee info (sales support agents, general manager). |

---

## 🔗 Relationships
- **Artist → Album** (One-to-Many)
- **Album → Track** (One-to-Many)
- **Genre / MediaType → Track** (Categorization)
- **Playlist ↔ Track** (Many-to-Many via PlaylistTrack)
- **Customer → Invoice → InvoiceLine** (Sales + Purchases)
- **Employee → Customer** (Support Representative relationship)

---

---

## Business Questions & Queries

---

## Level 1 — Easy

---

### Q1 — Who is the senior most employee based on job title?

**What this asks:** Find the employee at the top of the
organisational hierarchy using the levels column.
```sql
SELECT title, last_name, first_name
FROM employee
ORDER BY levels DESC
LIMIT 1;
```

**Finding:** A simple ORDER BY on the levels column surfaces
the most senior employee — useful for org chart validation
and access control decisions.

---

### Q2 — Which countries have the most invoices?

**What this asks:** Rank countries by total invoice volume
to identify where the business is most active.
```sql
SELECT COUNT(*) AS c, billing_country
FROM invoice
GROUP BY billing_country
ORDER BY c DESC;
```

**Finding:** Invoice volume is not evenly distributed globally
— a small number of countries account for the majority of
transactions, indicating where the core customer base sits.

---

### Q3 — What are the top 3 invoice values?

**What this asks:** Surface the highest individual transaction
values in the dataset.
```sql
SELECT total
FROM invoice
ORDER BY total DESC
LIMIT 3;
```

**Finding:** Identifies outlier high-value transactions —
useful for spotting premium customers or unusual purchases
worth investigating further.

---

### Q4 — Which city has the best customers?
*(Highest sum of invoice totals — for promotional planning)*

**What this asks:** Find the single city generating the most
revenue — to decide where to host a promotional music festival.
```sql
SELECT billing_city, SUM(total) AS InvoiceTotal
FROM invoice
GROUP BY billing_city
ORDER BY InvoiceTotal DESC
LIMIT 1;
```

**Finding:** One city stands out significantly in total revenue
— making it the clear choice for a targeted promotional event
to maximise attendance from the highest-value customer base.

---

### Q5 — Who is the best customer?
*(Customer who has spent the most money overall)*

**What this asks:** Identify the single highest-spending
customer across all transactions.
```sql
SELECT 
    customer.customer_id,
    first_name,
    last_name,
    SUM(total) AS total_spending
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
GROUP BY customer.customer_id
ORDER BY total_spending DESC
LIMIT 1;
```

**Finding:** A single customer can be identified as the top
spender — valuable for VIP retention strategies and
personalised marketing.

---

## Level 2 — Moderate

---

### Q1 — Find all Rock Music listeners
*(Email, name — ordered alphabetically)*

**What this asks:** Return contact details of every customer
who has purchased Rock genre tracks — for targeted outreach.

**Two methods used — subquery vs direct join:**

**Method 1 — Subquery approach:**
```sql
SELECT DISTINCT email, first_name, last_name
FROM customer
JOIN invoice 
    ON customer.customer_id = invoice.customer_id
JOIN invoiceline 
    ON invoice.invoice_id = invoiceline.invoice_id
WHERE track_id IN (
    SELECT track_id FROM track
    JOIN genre ON track.genre_id = genre.genre_id
    WHERE genre.name LIKE 'Rock'
)
ORDER BY email;
```

**Method 2 — Direct JOIN approach:**
```sql
SELECT DISTINCT 
    email        AS Email,
    first_name   AS FirstName,
    last_name    AS LastName,
    genre.name   AS Name
FROM customer
JOIN invoice    ON invoice.customer_id    = customer.customer_id
JOIN invoiceline ON invoiceline.invoice_id = invoice.invoice_id
JOIN track      ON track.track_id         = invoiceline.track_id
JOIN genre      ON genre.genre_id         = track.genre_id
WHERE genre.name LIKE 'Rock'
ORDER BY email;
```

**Finding:** Both methods return identical results. The direct
JOIN approach is more readable and performs better at scale —
subqueries with IN can be slower on large datasets because
they execute the inner query for every row.

---

### Q2 — Top 10 rock bands by track count
*(Artists invited to the promotional event)*

**What this asks:** Identify the most prolific Rock artists
in the catalogue by number of tracks — to prioritise
artist invitations for a promotional event.
```sql
SELECT 
    artist.artist_id,
    artist.name,
    COUNT(artist.artist_id) AS number_of_songs
FROM track
JOIN album  ON album.album_id   = track.album_id
JOIN artist ON artist.artist_id = album.artist_id
JOIN genre  ON genre.genre_id   = track.genre_id
WHERE genre.name LIKE 'Rock'
GROUP BY artist.artist_id
ORDER BY number_of_songs DESC
LIMIT 10;
```

**Finding:** Rock track count is heavily concentrated in a
small number of artists — the top 10 bands represent a
disproportionate share of the Rock catalogue, making them
the natural priority for event invitations.

---

### Q3 — Tracks longer than average song length

**What this asks:** Return all tracks that exceed the dataset
average duration — ordered longest first.

**Why a subquery is used here:**
The average needs to be calculated across the entire dataset
first, then used as a filter threshold. A subquery in the
WHERE clause handles this cleanly in a single query.
```sql
SELECT name, miliseconds
FROM track
WHERE miliseconds > (
    SELECT AVG(miliseconds) AS avg_track_length
    FROM track
)
ORDER BY miliseconds DESC;
```

**Finding:** A significant portion of tracks exceed average
length — useful for understanding catalogue composition and
identifying which formats or genres tend toward longer
runtime.

---

## Level 3 — Advanced

---

### Q1 — How much did each customer spend on the
best-selling artist?

**Why this is complex:**
The Invoice total covers multiple products in one transaction.
To get per-artist spending accurately, you need InvoiceLine
level data — calculating unit_price × quantity per track,
then tracing each track back through Album to Artist.
Using Invoice.total directly would give an inflated and
incorrect result.

**Approach:** Two-step CTE.
Step 1 — identify the best-selling artist by total revenue.
Step 2 — use that result to filter and calculate customer
spending on that specific artist only.
```sql
WITH best_selling_artist AS (
    SELECT 
        artist.artist_id    AS artist_id,
        artist.name         AS artist_name,
        SUM(invoice_line.unit_price * invoice_line.quantity)
                            AS total_sales
    FROM invoice_line
    JOIN track  ON track.track_id   = invoice_line.track_id
    JOIN album  ON album.album_id   = track.album_id
    JOIN artist ON artist.artist_id = album.artist_id
    GROUP BY 1
    ORDER BY 3 DESC
    LIMIT 1
)
SELECT 
    c.customer_id,
    c.first_name,
    c.last_name,
    bsa.artist_name,
    SUM(il.unit_price * il.quantity) AS amount_spent
FROM invoice i
JOIN customer            c   ON c.customer_id  = i.customer_id
JOIN invoice_line        il  ON il.invoice_id  = i.invoice_id
JOIN track               t   ON t.track_id     = il.track_id
JOIN album               alb ON alb.album_id   = t.album_id
JOIN best_selling_artist bsa ON bsa.artist_id  = alb.artist_id
GROUP BY 1, 2, 3, 4
ORDER BY 5 DESC;
```

**Finding:** Invoice-level totals are misleading for per-artist
spending analysis — drilling to InvoiceLine level reveals the
true distribution of spend across artists per customer.

---

### Q2 — Most popular genre per country
*(including ties where two genres share the top purchase count)*

**Why this is complex:**
A simple GROUP BY returns one row per country but silently
drops cases where two genres share the exact same purchase
count in a market. Both methods below handle ties correctly
and produce identical results — which validates the approach.

---

**Method 1 — ROW_NUMBER() with CTE:**

Uses a window function to rank genres within each country
by purchase count. Filtering WHERE RowNo <= 1 returns only
the top genre per country.
```sql
WITH popular_genre AS (
    SELECT 
        COUNT(invoice_line.quantity)    AS purchases,
        customer.country,
        genre.name,
        genre.genre_id,
        ROW_NUMBER() OVER (
            PARTITION BY customer.country
            ORDER BY COUNT(invoice_line.quantity) DESC
        )                               AS RowNo
    FROM invoice_line
    JOIN invoice  ON invoice.invoice_id   = invoice_line.invoice_id
    JOIN customer ON customer.customer_id = invoice.customer_id
    JOIN track    ON track.track_id       = invoice_line.track_id
    JOIN genre    ON genre.genre_id       = track.genre_id
    GROUP BY 2, 3, 4
    ORDER BY 2 ASC, 1 DESC
)
SELECT * FROM popular_genre WHERE RowNo <= 1;
```

---

**Method 2 — Recursive CTE:**

Calculates total purchases per genre per country in the
first CTE, finds the maximum purchase count per country
in the second, then joins both to return all genres that
match that maximum — correctly handling ties.
```sql
WITH RECURSIVE
    sales_per_country AS (
        SELECT 
            COUNT(*)        AS purchases_per_genre,
            customer.country,
            genre.name,
            genre.genre_id
        FROM invoice_line
        JOIN invoice  ON invoice.invoice_id   = invoice_line.invoice_id
        JOIN customer ON customer.customer_id = invoice.customer_id
        JOIN track    ON track.track_id       = invoice_line.track_id
        JOIN genre    ON genre.genre_id       = track.genre_id
        GROUP BY 2, 3, 4
        ORDER BY 2
    ),
    max_genre_per_country AS (
        SELECT 
            MAX(purchases_per_genre) AS max_genre_number,
            country
        FROM sales_per_country
        GROUP BY 2
        ORDER BY 2
    )
SELECT sales_per_country.*
FROM sales_per_country
JOIN max_genre_per_country
    ON sales_per_country.country = max_genre_per_country.country
WHERE sales_per_country.purchases_per_genre
    = max_genre_per_country.max_genre_number;
```

**Finding:** Genre preference varies significantly by country
— a single global catalogue strategy would underserve markets
where regional preferences diverge from the overall top genre.
Both methods return identical results confirming the
tie-handling logic is sound.

---

### Q3 — Highest spending customer per country
*(including ties where two customers share the top spend)*

**Why this is complex:**
Same tie-handling requirement as Q2 but applied to customer
spending by country. A basic GROUP BY with MAX() would return
one customer per country even when two customers spent
identical amounts — silently losing data. Both methods
handle this correctly.

---

**Method 1 — ROW_NUMBER() with CTE:**

Ranks customers within each billing country by total spend.
Returns only the top ranked customer per country.
```sql
WITH customer_with_country AS (
    SELECT 
        customer.customer_id,
        first_name,
        last_name,
        billing_country,
        SUM(total)      AS total_spending,
        ROW_NUMBER() OVER (
            PARTITION BY billing_country
            ORDER BY SUM(total) DESC
        )               AS RowNo
    FROM invoice
    JOIN customer ON customer.customer_id = invoice.customer_id
    GROUP BY 1, 2, 3, 4
    ORDER BY 4 ASC, 5 DESC
)
SELECT * FROM customer_with_country WHERE RowNo <= 1;
```

---

**Method 2 — Recursive CTE:**

Calculates total spending per customer per country in the
first CTE, finds the maximum spend per country in the second,
then joins to return all customers who match that maximum —
correctly surfacing tied results.
```sql
WITH RECURSIVE
    customer_with_country AS (
        SELECT 
            customer.customer_id,
            first_name,
            last_name,
            billing_country,
            SUM(total) AS total_spending
        FROM invoice
        JOIN customer ON customer.customer_id = invoice.customer_id
        GROUP BY 1, 2, 3, 4
        ORDER BY 2, 3 DESC
    ),
    country_max_spending AS (
        SELECT 
            billing_country,
            MAX(total_spending) AS max_spending
        FROM customer_with_country
        GROUP BY billing_country
    )
SELECT 
    cc.billing_country,
    cc.total_spending,
    cc.first_name,
    cc.last_name,
    cc.customer_id
FROM customer_with_country cc
JOIN country_max_spending ms
    ON cc.billing_country = ms.billing_country
WHERE cc.total_spending = ms.max_spending
ORDER BY 1;
```

**Finding:** Top spenders are not evenly distributed globally
— high-value customers cluster in specific markets, suggesting
those regions warrant priority attention for retention and
upsell efforts. Both methods return identical results
confirming the tie-handling logic works correctly.



---



