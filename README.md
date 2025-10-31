# 🎵 SQL Music Store Analysis

## 📌 Project Overview
This SQL project analyzes data from an **online music store database**.  
You will use SQL to answer business questions, perform data exploration, and derive insights to help the store understand customer behavior, playlist trends, and sales performance.

This project is ideal for **beginners**, covering:
- Writing basic to intermediate SQL queries
- Understanding relationships between tables
- Performing data analysis using relational databases

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

## 📊 Analysis Goals
By writing SQL queries, we aim to answer questions like:

- Who are the top customers?
- Which genre generates the most revenue?
- Which artists have the most tracks in playlists?
- What are the best-selling tracks and albums?

Examples of analysis tasks:
- Total sales by country
- Most popular genre by number of tracks purchased
- Employees generating the most business through customer accounts


## ✅ Outcome
By the end of this project, you will be able to:
- Analyze relational data using SQL
- Generate business insights from structured tables
- Apply SQL joins and aggregations confidently

---



