
# Data-Tools

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

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🚀 Live Demo <a name="live-demo"></a>

> Backend-only project. Interact via Supabase SQL editor.

* [Supabase Project Link](https://supabase.com/dashboard/project/octmhkzbzxsoaegmuaei/sql/3cf2fb04-a61c-4254-87aa-e725d2b6f0f9)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

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

3. Run example queries like:

```sql
-- Songs liked by Alice
SELECT u.username, s.title, a.name AS artist_name
FROM user_favorites uf
JOIN users u ON uf.user_id = u.user_id
JOIN songs s ON uf.song_id = s.song_id
JOIN artists a ON s.artist_id = a.artist_id
WHERE u.username = 'alice';
```

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

# 💾 Schema SQL <a name="schema-sql"></a>

<details>
<summary>Click to expand full schema.sql</summary>

```sql
-- Full schema and sample data (users, artists, songs, user_favorites)
-- See previous sections for full content
```

</details>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

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
```

</details>

### Most Favorited Songs

![](path/to/popular_songs_plot.png)

### Most Active Users

![](path/to/active_users_plot.png)

### Artist Performance Bubble Chart

![](path/to/artist_performance_plot.png)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 📖 Data Dictionary <a name="data-dictionary"></a>

**📖 Full Data Dictionary:** [Check it here](https://github.com/DENNIS-MURITHI/Data-Tools/blob/test_branch/data_dictionary.md)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 👥 Authors <a name="authors"></a>

👤 **Dennis Murithi**

* GitHub: [@dennismurithi](https://github.com/DENNIS-MURITHI)
* LinkedIn: [LinkedIn](https://www.linkedin.com/in/dennis-muthuri/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 🔭 Future Features <a name="future-features"></a>

* Front-end integration with music streaming app  
* Advanced analytics (top songs, popular artists, trends)  
* Playlists, ratings, and user-generated content  

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome. Open an issue or submit a pull request.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# ⭐️ Show your support <a name="support"></a>

If you like this project, give it a ⭐️ on GitHub!

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 🙏 Acknowledgements <a name="acknowledgements"></a>

* [Supabase](https://supabase.com/) for PostgreSQL hosting and testing  
* [draw.io](https://draw.io) for ERD creation inspiration  

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# ❓ FAQ <a name="faq"></a>

1. How do I run this project?  
2. What dependencies are needed?  
3. Can I connect using MySQL Workbench? (No, PostgreSQL/Supabase only)  

# 📝 License <a name="license"></a>

This project is licensed under MIT License - see [LICENSE](LICENSE) for details.

