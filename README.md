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



