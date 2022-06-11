# Learn PostgreSQL

Source: [Learn-SQL](https://github.com/WebDevSimplified/Learn-SQL)

Source: [Reddit-SQL-Join-Chart](https://www.reddit.com/r/SQL/comments/aysflk/sql_join_chart_custom_poster_size/)

### Quickstart

Run [script](./resources/script.sql) in `psql` with `\i <path.sql>`

---

### SQL Joins

<p align="center">
  <img src="resources/images/sql-join-chart-custom-poster-size-sql.png" title="sql-joins">
</p>

---

### Exercises

### 1. Select the Oldest Album

Make sure to only return one result from this query, and that you are not returning any albums that do not have a release year.

<details>
   <summary>solution</summary>

```sql
  SELECT * FROM albums WHERE release_year IS NOT NULL ORDER BY release_year LIMIT 1;

  id |          name          | release_year | band_id
  ----+------------------------+--------------+---------
  5  | ...And Justice for All |         1988 |       2
  (1 row)
```
</details>

### 2. Get all Bands that have Albums

There are multiple different ways to solve this problem, but they will all involve a join.

Return the band name as `Band Name`.

<details>
   <summary> 1. solution</summary>

```sql
  SELECT bands.name AS "Band Name"
  FROM bands
  INNER JOIN albums
  ON bands.id = albums.band_id
  GROUP BY bands.name
  ORDER BY bands.name;

       Band Name
  -------------------
  Death
  Metallica
  Seventh Wonder
  The Ocean
  Van Canto
  Within Temptation
  (6 rows)
```
</details>

<details>
   <summary>2. solution</summary>

```sql
  SELECT DISTINCT(bands.name) AS "Band Name"
  FROM bands
  INNER JOIN albums
  ON bands.id = albums.band_id
  ORDER BY bands.name;

       Band Name
  -------------------
  Death
  Metallica
  Seventh Wonder
  The Ocean
  Van Canto
  Within Temptation
  (6 rows)
```
</details>

### 3. Get all Bands that have No Albums

This is very similar to #2 but will require more than just a join.

Return the band name as `Band Name`.

<details>
   <summary>solution</summary>

```sql
  SELECT b.name AS "Band Name"
  FROM bands AS b
  LEFT OUTER JOIN albums AS a
  ON b.id = a.band_id
  WHERE a.id IS NULL;

     Band Name
  ---------------
  Dream Theater
  (1 row)
```
</details>

### 4. Get the Longest Album

This problem sounds a lot like #1 but the solution is quite a bit different. I would recommend looking up the SUM aggregate function.

Return the album name as `Name`, the album release year as` Release Year`, and the album length as `Duration`.

<details>
   <summary>1. solution</summary>

```sql
  SELECT a.name AS "Name", a.release_year AS "Release Year", s.length AS "Duration"
  FROM albums AS a
  INNER JOIN (SELECT album_id, SUM(length) AS "length" FROM songs GROUP BY album_id) AS s
  ON a.id = s.album_id
  ORDER BY "Duration" DESC
  LIMIT 1;

        Name      | Release Year |     Duration
  ----------------+--------------+------------------
  Death Magnetic  |         2008 | 74.7666666666667
  (1 row)
```
</details>

<details>
   <summary>2. solution</summary>

```sql
  SELECT a.name AS "Name", a.release_year AS "Release Year", SUM(s.length) AS "Duration"
  FROM albums AS a
  INNER JOIN songs AS s
  ON a.id = s.album_id
  GROUP BY a.name, a.release_year
  ORDER BY "Duration" DESC
  LIMIT 1;

        Name      | Release Year |     Duration
  ----------------+--------------+------------------
  Death Magnetic  |         2008 | 74.7666666666667
  (1 row)
```
</details>

### 5. Get the Average Length of all Songs

Return the average length as `Average Song Duration`.

<details>
   <summary>solution</summary>

```sql
  SELECT AVG(length) AS "Average Song Duration" FROM songs;

   Average Song Duration
  -----------------------
        5.35247252747253
  (1 row)
```
</details>

### 6. Select the longest Song off each Album

Return the album name as `Album`, the album release year as `Release Year`, and the longest song length as `Duration`.

<details>
   <summary>solution</summary>

```sql
  SELECT a.name AS "Album", a.release_year AS "Release Year", MAX(s.length) AS "Duration"
  FROM albums AS a
  INNER JOIN songs as s
  ON a.id = s.album_id
  GROUP BY a.name, a.release_year
  ORDER BY "Duration" DESC;

              Album            | Release Year |     Duration
  -----------------------------+--------------+------------------
  The Great Escape             |         2010 | 30.2333333333333
  Death Magnetic               |         2008 | 9.96666666666667
  ...And Justice for All       |         1988 | 9.81666666666667
  Tiara                        |         2018 |              9.5
  Mercy Falls                  |         2008 | 9.48333333333333
  Anthropocentric              |         2010 |              9.4
  Pelagial                     |         2013 | 9.28333333333333
  Master of Puppets            |              | 8.58333333333333
  The Sound of Perseverance    |         1998 | 8.43333333333333
  Tribe of Force               |         2010 | 8.38333333333333
  Heliocentric                 |         2010 | 7.48333333333333
  Enter                        |         1997 |             7.25
  Break the Silence            |         2011 |             6.15
  Resist                       |         2018 |             5.85
  The Unforgiving              |         2011 | 5.66666666666667
  A Storm to Come              |         2006 | 5.21666666666667
  Individual Thought Patterns  |         1993 | 4.81666666666667
  Human                        |         1991 |             4.65
  (18 rows)
```
</details>

### 7. Get the number of Songs for each Band

This is one of the toughest question on the list. It will require you to chain together two joins instead of just one.

Return the band name as `Band`, the number of songs as `Number of Songs`.

<details>
   <summary>solution</summary>

```sql
  SELECT b.name AS "Band", COUNT(s.id) AS "Number of Songs"
  FROM bands AS b
  INNER JOIN albums AS a
  ON b.id = a.band_id
  INNER JOIN songs AS s
  ON a.id = s.album_id
  GROUP BY b.id
  ORDER BY "Number of Songs";

         Band        | Number of Songs
  -------------------+-----------------
  Death              |              27
  Metallica          |              27
  Within Temptation  |              30
  The Ocean          |              31
  Van Canto          |              32
  Seventh Wonder     |              35
  (6 rows)
```
</details>
