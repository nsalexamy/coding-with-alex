# Database Implementation Plan

## Overview

This plan is for teaching basic concepts and operations of databases to a grade 9 student learning Python at school. We have installed PostgreSQL 17 using Docker.

We will create two regular tables and one mapping (junction) table to demonstrate relationships between data:

- **friends** — stores information about friends
- **games** — stores information about video games
- **friend_game_mapping** — links friends to the games they play (many-to-many relationship)

---

## Table Definitions

### friends

| Column           | Type         | Notes                        |
|------------------|--------------|------------------------------|
| id               | SERIAL       | Primary key, auto-increment  |
| first_name       | VARCHAR(50)  | Required                     |
| last_name        | VARCHAR(50)  | Required                     |
| date_of_birth    | DATE         |                              |
| gender           | VARCHAR(10)  | e.g. 'Male', 'Female', 'Other' |
| timezone         | VARCHAR(50)  | e.g. 'America/Toronto'       |
| discord_username | VARCHAR(50)  | Unique handle                |

### games

| Column           | Type         | Notes                        |
|------------------|--------------|------------------------------|
| id               | SERIAL       | Primary key, auto-increment  |
| title            | VARCHAR(100) | Required                     |
| company_name     | VARCHAR(100) | Publisher or developer       |
| available_online | BOOLEAN      | Whether it supports online play |
| genre            | VARCHAR(50)  | e.g. 'Battle Royale', 'MOBA' |
| release_date     | DATE         |                              |

### friend_game_mapping

| Column    | Type    | Notes                        |
|-----------|---------|------------------------------|
| friend_id | INTEGER | Foreign key → friends(id)    |
| game_id   | INTEGER | Foreign key → games(id)      |

Primary key is the combination of (friend_id, game_id).

---

## Table Schemas (SQL)

```sql
CREATE TABLE friends (
    id               SERIAL PRIMARY KEY,
    first_name       VARCHAR(50)  NOT NULL,
    last_name        VARCHAR(50)  NOT NULL,
    date_of_birth    DATE,
    gender           VARCHAR(10),
    timezone         VARCHAR(50),
    discord_username VARCHAR(50)
);

CREATE TABLE games (
    id               SERIAL PRIMARY KEY,
    title            VARCHAR(100) NOT NULL,
    company_name     VARCHAR(100),
    available_online BOOLEAN      DEFAULT FALSE,
    genre            VARCHAR(50),
    release_date     DATE
);

CREATE TABLE friend_game_mapping (
    friend_id INTEGER NOT NULL REFERENCES friends(id) ON DELETE CASCADE,
    game_id   INTEGER NOT NULL REFERENCES games(id)   ON DELETE CASCADE,
    PRIMARY KEY (friend_id, game_id)
);
```

---

## Sample Data (SQL)

```sql
-- Insert friends
INSERT INTO friends (first_name, last_name, date_of_birth, gender, timezone, discord_username) VALUES
    ('Alex',    'Kim',     '2011-03-15', 'Male',   'America/Toronto',    'alex_k'),
    ('Jordan',  'Lee',     '2011-07-22', 'Female', 'America/Vancouver',  'jordan_lee99'),
    ('Sam',     'Park',    '2010-11-08', 'Male',   'America/New_York',   'sampark_gamer'),
    ('Taylor',  'Nguyen',  '2011-01-30', 'Female', 'America/Chicago',    'taylor_n'),
    ('Chris',   'Patel',   '2010-09-14', 'Male',   'America/Los_Angeles','chris_p_plays');

-- Insert games

INSERT INTO games (title, company_name, available_online, genre, release_date) VALUES
    ('Minecraft',        'Mojang Studios',       TRUE,  'Sandbox',       '2011-11-18'),
    ('Fortnite',         'Epic Games',           TRUE,  'Battle Royale', '2017-07-25'),
    ('Roblox',           'Roblox Corporation',   TRUE,  'Sandbox',       '2006-09-01'),
    ('Valorant',         'Riot Games',           TRUE,  'FPS',           '2020-06-02'),
    ('League of Legends','Riot Games',           TRUE,  'MOBA',          '2009-10-27'),
    ('Apex Legends',     'Respawn Entertainment',TRUE,  'Battle Royale', '2019-02-04'),
    ('Elden Ring',       'FromSoftware',         FALSE, 'Action RPG',    '2022-02-25'),
    ('Stardew Valley',   'ConcernedApe',         TRUE,  'Simulation',    '2016-02-26');

-- Insert friend-game mappings
INSERT INTO friend_game_mapping (friend_id, game_id) VALUES
    (1, 1), -- Alex     plays Minecraft
    (1, 2), -- Alex     plays Fortnite
    (1, 4), -- Alex     plays Valorant
    (2, 1), -- Jordan   plays Minecraft
    (2, 3), -- Jordan   plays Roblox
    (2, 8), -- Jordan   plays Stardew Valley
    (3, 2), -- Sam      plays Fortnite
    (3, 6), -- Sam      plays Apex Legends
    (3, 5), -- Sam      plays League of Legends
    (4, 3), -- Taylor   plays Roblox
    (4, 8), -- Taylor   plays Stardew Valley
    (4, 5), -- Taylor   plays League of Legends
    (5, 4), -- Chris    plays Valorant
    (5, 6), -- Chris    plays Apex Legends
    (5, 7); -- Chris    plays Elden Ring
```

---

## Key Concepts to Cover

1. **Primary Keys** — uniquely identify each row (e.g. `id` column)
2. **Foreign Keys** — link rows across tables (e.g. `friend_id` in the mapping table)
3. **Many-to-Many Relationships** — a friend can play many games; a game can be played by many friends; the mapping table captures this
4. **Basic Queries** — `SELECT`, `WHERE`, `ORDER BY`
5. **JOINs** — combining data from multiple tables
6. **Aggregations** — `COUNT`, `GROUP BY` (e.g. how many friends play each game)

---

## Example Queries

```sql
-- List all friends
SELECT * FROM friends ORDER BY last_name;

-- List all games by genre
SELECT title, genre, company_name FROM games ORDER BY genre, title;

-- Find which games Alex plays
SELECT g.title, g.genre
FROM games g
JOIN friend_game_mapping fgm ON g.id = fgm.game_id
JOIN friends f ON f.id = fgm.friend_id
WHERE f.first_name = 'Alex';

-- Count how many friends play each game
SELECT g.title, COUNT(fgm.friend_id) AS player_count
FROM games g
JOIN friend_game_mapping fgm ON g.id = fgm.game_id
GROUP BY g.title
ORDER BY player_count DESC;

-- Find friends who play the same game as Alex
SELECT DISTINCT f.first_name, f.last_name
FROM friends f
JOIN friend_game_mapping fgm ON f.id = fgm.friend_id
WHERE fgm.game_id IN (
    SELECT game_id FROM friend_game_mapping
    JOIN friends ON friends.id = friend_game_mapping.friend_id
    WHERE friends.first_name = 'Alex'
)
AND f.first_name != 'Alex';
```
