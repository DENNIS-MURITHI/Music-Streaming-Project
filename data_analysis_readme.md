
# Data-Analysis

<div align="center">
  <img width="200" height="200" alt="Music Streaming Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h2><b>Music Streaming Project </b></h2>
</div>

# 📗 Table of Contents

* [📖 About the Project](#about-project)
  * [🛠 Built With](#built-with)
  * [Key Features](#key-features)
  * [🚀 Live Demo](#live-demo)
* [💻 Getting Started](#getting-started)
  * [Prerequisites](#prerequisites)
  * [Setup](#setup)
  * [Usage](#usage)
  * [Connecting from Posit to Supabase](#posit-supabase-connection)
* [💾 Schema SQL](#schema-sql)
* [📊 R Data Analysis](#r-data-analysis)
* [📖 Data Dictionary](#data-dictionary)
* [👥 Authors](#authors)
* [🔭 Future Features](#future-features)
* [🤝 Contributing](#contributing)
* [⭐️ Show your support](#support)
* [🙏 Acknowledgements](#acknowledgements)
* [❓ FAQ](#faq)
* [📝 License](#license)

---

# 📖 About the Project <a name="about-project"></a>

> This project models a music streaming platform's backend database. It includes users, artists, songs, and user favorites, enabling functionalities like song liking, artist categorization, and user engagement tracking. Additionally, we demonstrate R-based data analysis of user activity and artist performance.

## 🛠 Built With <a name="built-with"></a>

### Tech Stack

<details>
  <summary>Database & Hosting</summary>
  <ul>
    <li><a href="https://supabase.com">Supabase (PostgreSQL)</a> – backend database for tables, data storage, and queries</li>
  </ul>
</details>

<details>
  <summary>SQL Queries</summary>
  <ul>
    <li>Database schema creation, data insertion, and example queries</li>
  </ul>
</details>

<details>
  <summary>R Data Analysis</summary>
  <ul>
    <li><a href="https://posit.co/">Posit / RStudio</a> for connecting to Supabase and performing exploratory data analysis (EDA)</li>
    <li>Libraries: DBI, dplyr, ggplot2 for querying and visualization</li>
  </ul>
</details>

### Key Features <a name="key-features"></a>

* Users can like multiple songs and track favorites.
* Songs are linked to artists, supporting multiple songs per artist.
* SQL queries for analyzing top songs, active users, and most popular artists.
* R-based visualizations for song popularity, user activity, and artist performance.

<p align="right"><a href="#about-project">back to top</a></p>

## 🚀 Live Demo <a name="live-demo"></a>

> Backend-only project. Interact via Supabase SQL editor.

* [Supabase Project Link](https://supabase.com/dashboard/project/octmhkzbzxsoaegmuaei/sql/3cf2fb04-a61c-4254-87aa-e725d2b6f0f9)

<p align="right"><a href="#about-project">back to top</a></p>

---

# 💻 Getting Started <a name="getting-started"></a>

### Prerequisites

* Supabase account
* Posit / RStudio
* R packages: DBI, dplyr, ggplot2

### Setup

Clone the repository:

```bash
git clone https://github.com/DENNIS-MURITHI/music-streaming-database.git
cd music-streaming-database
```

### Usage

1. Open Supabase and create a new project.
2. Access the SQL editor and execute `schema.sql` to create tables and insert sample data:

```sql
\i schema.sql
```

3. A quick taste of how R posit code would look like:

```r
# Load connection
source("connect_db.R")
con <- connect_db()

# Run your query
query <- "
  SELECT u.username, s.title, a.name AS artist_name
  FROM user_favorites uf
  JOIN users u ON uf.user_id = u.user_id
  JOIN songs s ON uf.song_id = s.song_id
  JOIN artists a ON s.artist_id = a.artist_id
  WHERE u.username = 'alice';
"

# Execute query and store results
alice_favorites <- dbGetQuery(con, query)

# View results
print(alice_favorites)
#outome : 
# source("/cloud/project/alice_favorite.R")
#username              title artist_name
#1    alice Programmers choice   Sauti Sol

```
# Outcome upon running the code

<img width="1366" height="634" alt="image" src="https://github.com/user-attachments/assets/586fef26-f522-47cc-9531-11af15845984" />
---

### Connecting from Posit to Supabase <a name="posit-supabase-connection"></a>

1. Install required R packages:

```r
install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))
```

2. Create a `connect_db.R` file:

```r
library(DBI)
connect_db <- function() {
  dbConnect(
    RPostgres::Postgres(),
    dbname = "your_dbname",
    host = "your_project.supabase.co",
    port = 5432,
    user = "your_username",
    password = "your_password",
    sslmode = "require"
  )
}
```

3. Use this connection in R scripts:

```r
source("connect_db.R")
con <- connect_db()
dbListTables(con)
```

---
# Outcome after establishing connection
<img width="1366" height="680" alt="establish connection to supabase databse in posit" src="https://github.com/user-attachments/assets/6b9d9c3f-9387-46d7-b15e-0f880ba6f362" />



# 💾 Must Have Schema SQL <a name="schema-sql"></a>


<details>
  <summary>Click to expand the full schema.sql that you must run in supabase before you create a conection to posit studi</summary>

```sql
-- Users table
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    signup_date DATE NOT NULL
);

-- Artists table
CREATE TABLE artists (
    artist_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    genre VARCHAR(50)
);

-- Songs table
CREATE TABLE songs (
    song_id SERIAL PRIMARY KEY,
    title VARCHAR(150) NOT NULL,
    artist_id INT REFERENCES artists(artist_id),
    release_year INT,
    duration_seconds INT
);

-- User favorites table
CREATE TABLE user_favorites (
    favorite_id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(user_id),
    song_id INT REFERENCES songs(song_id),
    favorited_at DATE DEFAULT CURRENT_DATE
);

-- Users
INSERT INTO users (username, email, signup_date) VALUES
('alice', 'alice@example.com', '2025-01-10'),
('bob', 'bob@example.com', '2025-02-20'),
('carol', 'carol@example.com', '2025-03-15'),
('david', 'david@example.com', '2025-04-05'),
('evelyne', 'evelyne@example.com', '2025-05-12'),
('francis', 'francis@example.com', '2025-06-01'),
('grace', 'grace@example.com', '2025-06-10'),
('dennis', 'dennis@example.com', '2025-06-15'),
('ken', 'ken@example.com', '2025-06-20'),
('sharon', 'sharon@example.com', '2025-06-25');

-- Artists
INSERT INTO artists (name, genre) 
VALUES
('Sauti Sol', 'Afro-Pop'),         
('Nyashinski', 'Hip-Hop'),         
('Diamond Platnumz', 'Bongo Flava'), 
('Willy Paul', 'Gospel/Pop'),      
('Davido', 'Afrobeat'),            
('Bien', 'Afro-Pop'),              
('Bahati', 'Gospel/Pop'),          
('Iyanii', 'Afrobeat');

-- Songs
INSERT INTO songs (title, artist_id, release_year, duration_seconds) 
VALUES
('Suzanna', 1, 2019, 240),
('Kuliko Jana', 1, 2016, 220),
('Malaika', 2, 2016, 215),
('Now You Know', 2, 2018, 210),
('Jeje', 3, 2020, 250),
('African Beauty', 3, 2019, 240),
('I Do', 4, 2018, 230),
('Una', 4, 2020, 225),
('Fall', 5, 2017, 245),
('If', 5, 2017, 230),
('Nairobi Love', 6, 2021, 215),
('Upendo', 7, 2019, 220),
('Sweet Melody', 8, 2020, 210);

-- User favorites
INSERT INTO user_favorites (user_id, song_id) 
VALUES
(1, 1), (1, 3), (2, 5), (2, 2), (3, 6), (4, 7), (5, 10), (6, 9), (7, 12), (8, 11), (9, 4), (10, 8);

-- Example query
SELECT * FROM user_favorites;
```

```sql
-- List all songs liked by Alice
SELECT u.username, s.title, a.name AS artist_name
FROM user_favorites uf
JOIN users u ON uf.user_id = u.user_id
JOIN songs s ON uf.song_id = s.song_id
JOIN artists a ON s.artist_id = a.artist_id
WHERE u.username = 'alice';
```

```sql
-- Find all songs by 'Sauti Sol'
SELECT s.title, s.release_year
FROM songs s
JOIN artists a ON s.artist_id = a.artist_id
WHERE a.name = 'Sauti Sol';
```

```sql
-- Most popular artist (by total favorites across their songs)
-- Aggregates favorites to rank artists
-- Example: Who is the most liked artist overall?
SELECT a.name, COUNT(uf.user_id) AS total_favorites
FROM artists a
JOIN songs s ON a.artist_id = s.artist_id
LEFT JOIN user_favorites uf ON s.song_id = uf.song_id
GROUP BY a.artist_id
ORDER BY total_favorites DESC;
```

</details>

<p align="right"><a href="#about-project">back to top</a></p>

---

# 📊 R Data Analysis <a name="r-data-analysis"></a>

<details>
<summary>Click to expand full R analysis code</summary>

```r
source("connect_db.R")
library(DBI)
library(dplyr)
library(ggplot2)

con <- connect_db()

# Popular songs
popular_songs <- dbGetQuery(con, "
  SELECT s.title, a.name AS artist, COUNT(uf.song_id) AS total_favorites
  FROM user_favorites uf
  JOIN songs s ON uf.song_id = s.song_id
  JOIN artists a ON s.artist_id = a.artist_id
  GROUP BY s.title, a.name
  ORDER BY total_favorites DESC;
")
ggplot(popular_songs, aes(x = reorder(title, total_favorites), y = total_favorites, fill = artist)) +
  geom_col() + coord_flip() + theme_minimal() +
  labs(title='Most Favorited Songs', x='Song', y='Favorites Count')

# Active users
active_users <- dbGetQuery(con, "
  SELECT u.username, COUNT(uf.user_id) AS favorites_count
  FROM user_favorites uf
  JOIN users u ON uf.user_id = u.user_id
  GROUP BY u.username
  ORDER BY favorites_count DESC;
")
ggplot(active_users, aes(x = reorder(username, favorites_count), y = favorites_count, fill = username)) +
  geom_col(show.legend=FALSE) + coord_flip() + theme_minimal() +
  labs(title='Most Active Users', x='Username', y='Number of Favorites')

# Artist performance
agg_artist <- dbGetQuery(con, "
  SELECT a.name AS artist,
         COUNT(DISTINCT s.song_id) AS n_songs,
         COUNT(uf.song_id) AS total_favorites
  FROM songs s
  JOIN artists a ON s.artist_id = a.artist_id
  LEFT JOIN user_favorites uf ON uf.song_id = s.song_id
  GROUP BY a.name
")
agg_artist <- agg_artist %>% mutate(avg_fav_per_song = total_favorites / pmax(n_songs,1))
ggplot(agg_artist, aes(x=n_songs, y=avg_fav_per_song, size=total_favorites, label=artist)) +
  geom_point(alpha=0.7, color='steelblue') + geom_text(vjust=-1, size=3) + theme_minimal() +
  labs(title='Artists: Breadth vs. Popularity', subtitle='Comparing number of songs to avg favorites per song',
       x='Number of Songs', y='Average Favorites per Song', size='Total Favorites')

# Number of Artists per genre

artists <- dbGetQuery(con, "SELECT * FROM artists;")
print(artists)

library(ggplot2)
library(dplyr)

# Count how many artists per genre
genre_counts <- artists %>%
  dplyr::count(genre)

# Plot as vertical bar chart
ggplot(genre_counts, aes(x = reorder(genre, n), y = n, fill = genre)) +
  geom_col(show.legend = FALSE, width = 0.7) +
  labs(
    title = "🎶 Number of Artists per Genre",
    x = "Genre",
    y = "Number of Artists"
  ) +
  theme_minimal(base_size = 14) +
  theme(
    plot.title = element_text(face = "bold", hjust = 0.5),
    axis.text.x = element_text(angle = 25, hjust = 1)
  )


```

</details>

### Most Favorited Songs
<img width="1366" height="630" alt="plotting most favorite songs4" src="https://github.com/user-attachments/assets/a00864a0-99b8-4b2a-8e77-4cdfe6e73caa" />

### Most Active Users

<img width="1363" height="628" alt="most active user5" src="https://github.com/user-attachments/assets/12d08288-53ce-4880-8c05-ff0382909a74" />


### Artist Performance Bubble Chart
<img width="1366" height="686" alt="image" src="https://github.com/user-attachments/assets/e004292a-1f80-4ad9-97cf-b56b57af8339" />

### Number of Artists per genre

<img width="1366" height="680" alt="image" src="https://github.com/user-attachments/assets/dfcdd318-6e0e-4f7e-8416-cb5bf4001f23" />


<p align="right"><a href="#about-project">back to top</a></p>

---

# 📖 Data Dictionary <a name="data-dictionary"></a>

**📖 Full Data Dictionary:** [Check it here](https://github.com/DENNIS-MURITHI/Data-Tools/blob/test_branch/data_dictionary.md)

<p align="right"><a href="#about-project">back to top</a></p>

---

# 👥 Authors <a name="authors"></a>

👤 **Dennis Murithi**

* GitHub: [@dennismurithi](https://github.com/DENNIS-MURITHI)
* LinkedIn: [LinkedIn](https://www.linkedin.com/in/dennis-muthuri/)

<p align="right"><a href="#about-project">back to top</a></p>

---

# 🔭 Future Features <a name="future-features"></a>

* Front-end integration with music streaming app  
* Advanced analytics (top songs, popular artists, trends)  
* Playlists, ratings, and user-generated content
* 
<p align="right"><a href="#about-project">back to top</a></p>

---

# 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome. Open an issue or submit a pull request.

<p align="right"><a href="#about-project">back to top</a></p>

---

# ⭐️ Show your support <a name="support"></a>

If you like this project, give it a ⭐️ on GitHub!

<p align="right"><a href="#about-project">back to top</a></p>

---

# 🙏 Acknowledgements <a name="acknowledgements"></a>

* [Supabase](https://supabase.com/) for PostgreSQL hosting and testing  
* [Posit](https://docs.posit.co/connect/) Connect Documentation   

<p align="right"><a href="#about-project">back to top</a></p>

---

# ❓ FAQ <a name="faq"></a>

**1. How do I run this project in Posit?**  
Open the repository in **Posit (RStudio)**, install dependencies, and run the R scripts step by step.  
Make sure your Supabase credentials are set correctly in `connect_db.R`.

**2. What dependencies are needed?**  
Install the following R packages:  
```r
install.packages(c("DBI", "RPostgres", "dplyr", "ggplot2"))
```
### 3. Can I use MySQL or other databases?  
❌ **No.** This project connects only to **Supabase (PostgreSQL)** for consistency and compatibility with R and Posit.

---

### 4. How do I connect Posit to Supabase?  
Use the `DBI` and `RPostgres` packages along with your Supabase credentials found in:  
**Supabase → Project Settings → Database → Connection Info**  

# 📝 License <a name="license"></a>

This project is licensed under MIT License - see [LICENSE](LICENSE) for details.

