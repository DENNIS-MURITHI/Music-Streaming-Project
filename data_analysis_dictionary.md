# 📖 Music Streaming Dataset Documentation (Posit + Supabase)

This data dictionary describes all tables, columns, and their meanings for the Music Streaming project, aligned with the R analysis we performed in Posit using Supabase.

---

## **Users Table**

| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| user_id     | SERIAL PRIMARY KEY | Unique ID for each user |
| username    | VARCHAR(50) NOT NULL | User's chosen display name |
| email       | VARCHAR(100) UNIQUE NOT NULL | User email address |
| signup_date | DATE NOT NULL | Date the user registered on the platform |

**Usage in R:**  
* Counting favorites per user (`active_users`)  
* Joining with `user_favorites` for exploratory analysis

---

## **Artists Table**

| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| artist_id   | SERIAL PRIMARY KEY | Unique ID for each artist |
| name        | VARCHAR(100) NOT NULL | Artist name |
| genre       | VARCHAR(50) | Artist music genre |

**Usage in R:**  
* Joining with `songs` to compute total favorites per artist (`agg_artist`)  
* Visualization of artist popularity vs. number of songs

---

## **Songs Table**

| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| song_id     | SERIAL PRIMARY KEY | Unique ID for each song |
| title       | VARCHAR(150) NOT NULL | Song title |
| artist_id   | INT REFERENCES artists(artist_id) | ID of the performing artist |
| release_year | INT | Year the song was released |
| duration_seconds | INT | Song length in seconds |

**Usage in R:**  
* Listing top favorited songs (`popular_songs`)  
* Aggregating by artist for popularity metrics

---

## **User_Favorites Table**

| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| favorite_id | SERIAL PRIMARY KEY | Unique ID for each favorite record |
| user_id     | INT REFERENCES users(user_id) | The user who favorited the song |
| song_id     | INT REFERENCES songs(song_id) | The song that was favorited |
| favorited_at | DATE DEFAULT CURRENT_DATE | Date the song was favorited |

**Usage in R:**  
* Counting total favorites per song (`popular_songs`)  
* Counting total favorites per user (`active_users`)  
* Aggregating favorites per artist for bubble chart (`agg_artist`)  

---

## **Relationships**

* **users → user_favorites**: One-to-many  
* **songs → user_favorites**: One-to-many  
* **artists → songs**: One-to-many  
* Many-to-many relationship between **users** and **songs** via `user_favorites`

---

## **Notes for Posit Analysis**

* Use `DBI` to connect to Supabase and retrieve tables.  
* Use `dplyr` for aggregation (`count`, `group_by`, `mutate`) and filtering.  
* Use `ggplot2` for visualization:
  - Popular songs bar chart  
  - Active users bar chart  
  - Artist performance bubble chart

---

## **💡 Tip: Why Posit is Great**

Posit (RStudio) makes this workflow smooth because:

* **Seamless DB integration:** Connect directly to Supabase/PostgreSQL using `DBI`.  
* **Powerful data wrangling:** `dplyr` allows quick aggregations and transformations.  
* **Visualization-ready:** `ggplot2` enables clean, publication-quality charts with minimal code.  
* **Reproducible workflows:** R scripts can be run repeatedly with updated data, ideal for analytics projects.  

---

# 🔗 Connecting Posit (RStudio) to Supabase

This guide explains how to connect Posit (RStudio) to your Supabase PostgreSQL database for analysis.

---

## **1. Install Required R Packages**

```r
install.packages("DBI")
install.packages("RPostgres")
install.packages("dplyr")
install.packages("ggplot2")
```

---

## **2. Obtain Supabase Database Credentials**

From Supabase → **Settings → Database → Connection info**:

- Host URL
- Port (default: 5432)
- Database name
- Username
- Password
- SSL mode (`require`)

---

## **3. Connect from Posit**

```r
library(DBI)
library(RPostgres)

connect_db <- function() {
  con <- dbConnect(
    RPostgres::Postgres(),
    dbname = "your_database_name",
    host = "your_host_url",
    port = 5432,
    user = "your_username",
    password = "your_password",
    sslmode = "require"
  )
  return(con)
}
```

---

## **4. Test the Connection**

```r
source("connect_db.R")
con <- connect_db()
dbListTables(con)
users <- dbGetQuery(con, "SELECT * FROM users LIMIT 5;")
print(users)
```

---

## **5. Use in Analysis**

* Aggregate with `dplyr` (`count`, `group_by`, `summarize`)  
* Visualize with `ggplot2` (popular songs, active users, artist metrics)  
* Query directly via `dbGetQuery()`

Example:

```r
popular_songs <- dbGetQuery(con, """
  SELECT s.title, a.name AS artist, COUNT(uf.song_id) AS total_favorites
  FROM user_favorites uf
  JOIN songs s ON uf.song_id = s.song_id
  JOIN artists a ON s.artist_id = a.artist_id
  GROUP BY s.title, a.name
  ORDER BY total_favorites DESC;
""")
```

---

## **💡 Tip**

Connecting Posit to Supabase allows real-time queries, reproducible analysis, and clean integration with `ggplot2` for professional charts.
---

