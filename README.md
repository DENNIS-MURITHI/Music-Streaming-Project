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
* [📊 ERD Diagram](#erd-diagram)
* [💾 Schema SQL](#schema-sql)
* [👥 Authors](#authors)
* [🔭 Future Features](#future-features)
* [🤝 Contributing](#contributing)
* [⭐️ Show your support](#support)
* [🙏 Acknowledgements](#acknowledgements)
* [❓ FAQ (OPTIONAL)](#faq)
* [📝 License](#license)

---

# 📖 About the Project <a name="about-project"></a>

> This project models a music streaming platform's backend database. It includes users, artists, songs, and user favorites, enabling functionalities like song liking, artist categorization, and user engagement tracking.

## 🛠 Built With <a name="built-with"></a>

### Tech Stack <a name="tech-stack"></a>

<details>
  <summary>Database & Hosting</summary>
  <ul>
    <li><a href="https://supabase.com">Supabase (PostgreSQL)</a> – used to create tables, manage schema, and test queries</li>
  </ul>
</details>

<details>
  <summary>SQL Queries</summary>
  <ul>
    <li>Database schema creation, data insertion, and example queries</li>
  </ul>
</details>

### Key Features <a name="key-features"></a>

* Users can like multiple songs and track favorites.
* Songs are linked to artists, supporting multiple songs per artist.
* Example SQL queries for listing favorites and filtering by artist.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🚀 Live Demo <a name="live-demo"></a>

> This project is backend-only. You can interact with it locally or via Supabase.

* [Supabase Project Link](https://supabase.com/dashboard/project/octmhkzbzxsoaegmuaei/sql/3cf2fb04-a61c-4254-87aa-e725d2b6f0f9)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 💻 Getting Started <a name="getting-started"></a>

### Prerequisites

* Supabase account
* SQL client (Supabase SQL editor)

### Setup

Clone this repository:

```bash
git clone https://github.com/DENNIS-MURITHI/music-streaming-database.git
cd music-streaming-database
```

### Usage

1. Open Supabase, create a new project, and access the SQL editor.
2. Execute the `schema.sql` file in Supabase SQL editor to create tables and insert sample data.

```sql
-- Execute schema.sql
\i schema.sql
```

3. Run example queries:

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

### Example Query Outputs

<div align="center">
**Output of query: List all songs liked by Alice**
  
<img width="1362" height="530" alt="image" src="https://github.com/user-attachments/assets/dd3a7612-af24-4053-9ff7-6e99b144bf3b" />

<br/>

**Output of query: Find all songs by Sauti Sol**

<img width="1366" height="544" alt="image for all songs by sauti sol" src="https://github.com/user-attachments/assets/207391bb-79cd-47a7-bc51-995aac5c56b7" />

<br/> 
**Output of query: Most popular artists**

<img width="1338" height="635" alt="image" src="https://github.com/user-attachments/assets/e75323bf-7db2-4b03-a519-fac47a419844" />


</div>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 📊 ERD Diagram <a name="erd-diagram"></a>

<div align="center">

<img width="100%" height="454" alt="image" src="https://github.com/user-attachments/assets/ee605b4d-0928-4287-a5af-c7da767cfddd" alt="Entity Relationship Diagram" />
</div>

<br/> 

* **Users table**: Stores user information.
* **Artists table**: Contains artist details.
* **Songs table**: Holds songs linked to artists.
* **User_favorites table**: Tracks which songs each user likes.

*Generated using [draw.io](https://draw.io) and verified in Supabase.*

# Data dictionary<a name="data dictionary"></a>

**📖 Data Dictionary:** [Check it here ](https://github.com/DENNIS-MURITHI/Data-Tools/blob/test_branch/data_dictionary.md)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 💾 Schema SQL <a name="schema-sql"></a>

<details>
  <summary>Click to expand the full schema.sql</summary>

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

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 👥 Authors <a name="authors"></a>

👤 **Dennis Murithi**

* GitHub: [@dennismurithi](https://github.com/DENNIS-MURITHI)
* LinkedIn: [LinkedIn](https://www.linkedin.com/in/dennis-muthuri/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 🔭 Future Features <a name="future-features"></a>

* [ ] Integrate with a front-end music streaming app
* [ ] Add advanced analytics (most liked songs, popular artists)
* [ ] Support playlists and song ratings

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome. Feel free to open an issue or submit a pull request.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# ⭐️ Show your support <a name="support"></a>

If you like this project, give it a ⭐️ on GitHub!

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

# 🙏 Acknowledgements <a name="acknowledgements"></a>

* Thanks to [Supabase](https://supabase.com/) for providing the PostgreSQL platform to generate and test the schema.
* Thanks to [draw.io](https://draw.io) for ERD visualization inspiration.

<p align="right">(<a href="#readme-top">back to top</a>)</p>
