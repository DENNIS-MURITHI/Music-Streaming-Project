# Data Dictionary for Music Streaming Database  

This data dictionary describes the structure and purpose of the database tables used in the Music Streaming project. It defines each table, its columns, data types, and relationships to help developers and analysts clearly understand the data model.  

---

## Table: `users`  
Stores information about people who use the platform.  

| Column Name  | Data Type           | Description                                   |
|--------------|---------------------|-----------------------------------------------|
| `user_id`    | SERIAL PRIMARY KEY  | Unique identifier for each user               |
| `username`   | VARCHAR(50)         | Display name of the user                      |
| `email`      | VARCHAR(100) UNIQUE | User's email address (must be unique)         |
| `signup_date`| DATE                | Date the user created an account              |

---

## Table: `artists`  
Holds data about music artists.  

| Column Name  | Data Type           | Description                                   |
|--------------|---------------------|-----------------------------------------------|
| `artist_id`  | SERIAL PRIMARY KEY  | Unique identifier for each artist             |
| `name`       | VARCHAR(100)        | Artist’s full/stage name                      |
| `genre`      | VARCHAR(50)         | Music genre (e.g., Afro-Pop, Afrobeat)        |

---

## Table: `songs`  
Contains details about songs in the catalog.  

| Column Name      | Data Type           | Description                                   |
|------------------|---------------------|-----------------------------------------------|
| `song_id`        | SERIAL PRIMARY KEY  | Unique identifier for each song               |
| `title`          | VARCHAR(150)        | Title of the song                             |
| `artist_id`      | INT FK → artists    | Links song to its artist                      |
| `release_year`   | INT                 | Year the song was released                    |
| `duration_seconds` | INT               | Song duration in seconds                      |

---

## Table: `user_favorites`  
Tracks songs favorited by users.  

| Column Name  | Data Type           | Description                                   |
|--------------|---------------------|-----------------------------------------------|
| `favorite_id`| SERIAL PRIMARY KEY  | Unique identifier for each favorite record    |
| `user_id`    | INT FK → users      | Links favorite to the user who liked the song |
| `song_id`    | INT FK → songs      | Links favorite to the liked song              |
| `favorited_at`| DATE               | Date when the user favorited the song         |

---

**Notes:**  
- Primary keys uniquely identify rows in each table.  
- Foreign keys (FK) connect relationships across tables (`songs → artists`, `user_favorites → users/songs`).  
- This schema supports queries like:  
  - "Find all songs liked by a user"  
  - "List all songs by a specific artist"  
  and many more queries based on your criterion. 
