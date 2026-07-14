# 🎵 Music Streaming Database — From Schema to Secure Access

<div align="center">
  <img width="314" height="285" alt="Supabase Logo" src="https://github.com/user-attachments/assets/20661293-a214-4004-9042-657102fb0710" />
  <br/>
  <h3><b>An evolving PostgreSQL/Supabase project — from data modeling to role-based security</b></h3>
</div>

---

## 📗 Table of Contents

* [📖 About the Project](#about-project)
* [🧭 Project Evolution](#project-evolution)
* [🛠 Built With](#built-with)
* [🚀 Live Demo](#live-demo)
* [💻 Getting Started](#getting-started)
* [📊 Phase 1: Data Model & ERD](#phase-1)
* [💾 Phase 1: Core SQL Queries](#phase-1-queries)
* [🛡 Phase 2: Row Level Security — Admin vs User Roles](#phase-2)
* [👥 Authors](#authors)
* [🔭 Future Features](#future-features)
* [🤝 Contributing](#contributing)
* [⭐️ Show your support](#support)
* [🙏 Acknowledgements](#acknowledgements)
* [❓ FAQ](#faq)
* [📝 License](#license)

---

## 📖 About the Project <a name="about-project"></a>

This project models the backend database of a **music streaming platform**, built entirely on **PostgreSQL and Supabase**. It started as a straightforward relational schema — users, artists, songs, and favorites — and was then extended into a **secure, role-aware system** using Row Level Security (RLS), separating what regular users can do from what admins can do.

Rather than treating these as two separate projects, this README documents the **full journey**: from the initial data model to the access-control layer built on top of it. It reflects how a real production database evolves — start with a working schema, then harden it for multiple user roles.

---

## 🧭 Project Evolution <a name="project-evolution"></a>

| Phase | Focus | What Was Added |
|---|---|---|
| **Phase 1 — Foundation** | Core data modeling | Users, Artists, Songs, and Favorites tables; ERD design; foundational SQL queries (joins, aggregations, ranking artists by popularity) |
| **Phase 2 — Security & Roles** | Access control | UUID-based auth via Supabase; Row Level Security policies; distinct **Admin** vs **User** privileges; tested CRUD operations under each role |

This phased structure mirrors how the project actually developed — Phase 1 established a working, queryable database; Phase 2 made it safe for multiple real users to interact with concurrently.

(back to top)

---

## 🛠 Built With <a name="built-with"></a>

- **PostgreSQL** — relational database engine
- **Supabase** — hosting, SQL editor, and authentication
- **Row Level Security (RLS) & Policies** — role-based access enforcement
- **SQL** — schema design, queries, and CRUD operations

(back to top)

---

## 🚀 Live Demo <a name="live-demo"></a>

This project is backend-only. Interact with it locally or via the [Supabase Dashboard](https://app.supabase.com).

(back to top)

---

## 💻 Getting Started <a name="getting-started"></a>

### Prerequisites
- Supabase account
- Basic SQL / PostgreSQL knowledge
- Git installed

### Setup

```bash
git clone https://github.com/DENNIS-MURITHI/Data-Tools.git
cd Data-Tools
```

### Usage

1. Open Supabase, create a new project, and access the SQL editor.
2. Run `schema.sql` to create the core tables and insert sample data (Phase 1).
3. Enable Row Level Security and apply the role policies (Phase 2):

```sql
ALTER TABLE user_favorites ENABLE ROW LEVEL SECURITY;
ALTER TABLE songs ENABLE ROW LEVEL SECURITY;
ALTER TABLE artists ENABLE ROW LEVEL SECURITY;
```

4. Apply the Admin and User policies detailed below.

(back to top)

---

## 📊 Phase 1: Data Model & ERD <a name="phase-1"></a>

The foundation of the project — four core tables:

- **Users** — stores user account information
- **Artists** — artist details
- **Songs** — linked to artists
- **User_favorites** — tracks which songs each user has liked

ERD generated using draw.io and verified against the live Supabase schema.

📖 Full [Data Dictionary](https://github.com/DENNIS-MURITHI/Data-Tools/blob/main/data_dictionary.md)

<details>
<summary>💾 Click to expand the full <code>schema.sql</code></summary>

```sql
-- ============================================================
-- Music Streaming Database — Schema Setup
-- Run this in the Supabase SQL editor (or any PostgreSQL client)
-- ============================================================

-- ------------------------------------------------------------
-- 1. Drop existing tables (safe re-run during development)
-- ------------------------------------------------------------
DROP TABLE IF EXISTS user_favorites CASCADE;
DROP TABLE IF EXISTS songs CASCADE;
DROP TABLE IF EXISTS artists CASCADE;
DROP TABLE IF EXISTS users CASCADE;

-- ------------------------------------------------------------
-- 2. Core Tables (Phase 1 — Foundation)
-- ------------------------------------------------------------

-- Users table (linked to Supabase Auth via UUID)
CREATE TABLE users (
    user_uuid   UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    username    VARCHAR(50) UNIQUE NOT NULL,
    email       VARCHAR(255) UNIQUE NOT NULL,
    role        VARCHAR(20) NOT NULL DEFAULT 'user' CHECK (role IN ('user', 'admin')),
    created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Artists table
CREATE TABLE artists (
    artist_id   SERIAL PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    country     VARCHAR(100),
    created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Songs table (linked to artists)
CREATE TABLE songs (
    song_id       SERIAL PRIMARY KEY,
    title         VARCHAR(150) NOT NULL,
    artist_id     INT NOT NULL REFERENCES artists(artist_id) ON DELETE CASCADE,
    release_year  INT,
    created_at    TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- User favorites table (many-to-many: users <-> songs)
CREATE TABLE user_favorites (
    favorite_id  SERIAL PRIMARY KEY,
    user_uuid    UUID NOT NULL REFERENCES users(user_uuid) ON DELETE CASCADE,
    song_id      INT NOT NULL REFERENCES songs(song_id) ON DELETE CASCADE,
    favorited_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE (user_uuid, song_id)
);

-- ------------------------------------------------------------
-- 3. Sample Data
-- ------------------------------------------------------------

INSERT INTO users (username, email, role) VALUES
    ('alice', 'alice@example.com', 'user'),
    ('bob', 'bob@example.com', 'user'),
    ('admin_dennis', 'dennis@example.com', 'admin');

INSERT INTO artists (name, country) VALUES
    ('Sauti Sol', 'Kenya'),
    ('Burna Boy', 'Nigeria'),
    ('Diamond Platnumz', 'Tanzania');

INSERT INTO songs (title, artist_id, release_year) VALUES
    ('Suzanna', 1, 2015),
    ('Melanin', 1, 2019),
    ('Last Last', 2, 2022),
    ('Jeje', 3, 2018);

INSERT INTO user_favorites (user_uuid, song_id)
SELECT user_uuid, 1 FROM users WHERE username = 'alice';

INSERT INTO user_favorites (user_uuid, song_id)
SELECT user_uuid, 3 FROM users WHERE username = 'bob';

-- ------------------------------------------------------------
-- 4. Row Level Security (Phase 2 — Roles & Access Control)
-- ------------------------------------------------------------

ALTER TABLE user_favorites ENABLE ROW LEVEL SECURITY;
ALTER TABLE songs ENABLE ROW LEVEL SECURITY;
ALTER TABLE artists ENABLE ROW LEVEL SECURITY;

-- ---- User Policies ----

CREATE POLICY "Users can view their own favorites"
ON user_favorites
FOR SELECT
USING (auth.uid() = user_uuid);

CREATE POLICY "Users can insert their own favorites"
ON user_favorites
FOR INSERT
WITH CHECK (auth.uid() = user_uuid);

CREATE POLICY "Users can delete their own favorites"
ON user_favorites
FOR DELETE
USING (auth.uid() = user_uuid);

CREATE POLICY "Users can read all songs"
ON songs
FOR SELECT
USING (true);

CREATE POLICY "Users can read all artists"
ON artists
FOR SELECT
USING (true);

-- ---- Admin Policies ----

CREATE POLICY "Admins can manage all favorites"
ON user_favorites
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));

CREATE POLICY "Admins can manage all songs"
ON songs
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));

CREATE POLICY "Admins can manage all artists"
ON artists
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));

-- ============================================================
-- End of schema.sql
-- ============================================================
```

</details>

(back to top)

---

## 💾 Phase 1: Core SQL Queries <a name="phase-1-queries"></a>

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
-- Find all songs by a specific artist
SELECT s.title, s.release_year
FROM songs s
JOIN artists a ON s.artist_id = a.artist_id
WHERE a.name = 'Sauti Sol';
```

```sql
-- Most popular artist by total favorites
SELECT a.name, COUNT(uf.user_id) AS total_favorites
FROM artists a
JOIN songs s ON a.artist_id = s.artist_id
LEFT JOIN user_favorites uf ON s.song_id = uf.song_id
GROUP BY a.artist_id
ORDER BY total_favorites DESC;
```

(back to top)

---

## 🛡 Phase 2: Row Level Security — Admin vs User Roles <a name="phase-2"></a>

Once the core schema was working, the project moved to a real-world concern: **not every user should have the same level of access.** This phase introduces UUID-based authentication via Supabase Auth and enforces least-privilege access through RLS policies.

### User Policies

```sql
-- Users can view only their own favorites
CREATE POLICY "Users can view their own favorites"
ON user_favorites
FOR SELECT
USING (auth.uid() = user_uuid);
```

```sql
-- Users can insert their own favorites
CREATE POLICY "Users can insert their own favorites"
ON user_favorites
FOR INSERT
WITH CHECK (auth.uid() = user_uuid);
```

```sql
-- Users can read all songs and artists
CREATE POLICY "Users can read all songs"
ON songs
FOR SELECT
USING (true);
```

### Admin Policies

```sql
-- Admins can manage all favorites, songs, and artists
CREATE POLICY "Admins can manage all favorites"
ON user_favorites
FOR ALL
USING (EXISTS (SELECT 1 FROM users WHERE user_uuid = auth.uid() AND role = 'admin'));
```

### Tested CRUD Behavior by Role

**User — adding a favorite:**
```sql
INSERT INTO user_favorites (user_uuid, song_id)
VALUES ('2d953804-5827-4f73-bfd5-41d83d53762f', 8);
```
![Output after Insert Favorite](https://github.com/user-attachments/assets/56d8d4d4-639e-4f17-baf3-0d501b8d3b96)

**User — removing a favorite:**
```sql
DELETE FROM user_favorites
WHERE user_uuid = '2d953804-5827-4f73-bfd5-41d83d53762f' AND song_id = 8;
```
![Output after user deletes a song](https://github.com/user-attachments/assets/794f8a04-ef31-4d8b-800b-8bf91cb6588f)

**Admin — updating a song title (not permitted for regular users):**
```sql
UPDATE songs SET title = 'Programmers choice' WHERE song_id = 1;
```
![Output after Admin updates song](https://github.com/user-attachments/assets/9f258a69-1202-41a1-b0c9-a7bf2722a3f8)

📖 Full write-up: [security_notes.md](https://github.com/DENNIS-MURITHI/Data-Tools/blob/main/security_notes.md)

(back to top)

---

## 👥 Authors <a name="authors"></a>

**Dennis Murithi Muthuri**
GitHub: [@DENNIS-MURITHI](https://github.com/DENNIS-MURITHI)
LinkedIn: [dennis-muthuri](https://www.linkedin.com/in/dennis-muthuri)

(back to top)

---

## 🔭 Future Features <a name="future-features"></a>

- Integrate with a front-end music streaming app
- Add analytics dashboards for popular songs/artists
- Implement audit logging for admin actions

(back to top)

---

## 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome. Feel free to open an issue or submit a pull request.

(back to top)

---

## ⭐️ Show your support <a name="support"></a>

If you like this project, give it a ⭐️ on GitHub!

(back to top)

---

## 🙏 Acknowledgements <a name="acknowledgements"></a>

- Supabase docs for SQL & RLS policies
- PostgreSQL official documentation
- draw.io for ERD visualization

(back to top)

---

## ❓ FAQ <a name="faq"></a>

**Q: How do I test RLS policies?**
A: Sign in as a User vs an Admin and attempt the same CRUD operations. Policies will restrict or allow access accordingly.

**Q: Can I extend this to a front-end?**
A: Yes — connect Supabase Auth with React, Next.js, or any front-end framework.

(back to top)

---

## 📝 License <a name="license"></a>

This project is licensed under the MIT License — see the LICENSE file for details.
