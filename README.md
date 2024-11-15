# spotify_data_exploration_sql

![spotify](https://github.com/SriSurya-DA/spotify_data_exploration/blob/main/spotify.jpg)

## Overview

This project entails a comprehensive analysis of a Spotify dataset, leveraging SQL to extract insights from a rich collection of attributes related to tracks, albums, and artists. The project encompasses a thorough end-to-end process, including:

Data Normalization: Transforming a denormalized dataset into a structured format, ensuring data consistency and integrity.
SQL Querying: Crafting and executing SQL queries of varying complexity (easy, medium, and advanced) to extract meaningful insights from the dataset.
Query Optimization: Fine-tuning query performance to ensure efficient data retrieval and analysis.
The primary objectives of this project are twofold:

Advanced SQL Skills Development: To hone expertise in writing complex SQL queries, data modeling, and performance optimization.
Insight Generation: To uncover valuable insights from the Spotify dataset, providing a deeper understanding of music trends, artist popularity, and user behavior.

```sql
-- Create table


DROP TABLE IF EXISTS spotify;

CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```

## EDA

```sql
-- To check total number of rows in the table
select count(*)
from spotify;

-- To check the number of Artist's

select count(distinct artist)
from spotify;

-- To check the number of Album's

select count (distinct album)
from spotify;

-- To check the available album_types

select distinct album_type
from spotify;

-- Maximum Duration

select max(duration_min)
from spotify;

-- Minimum Duration

select min(duration_min)
from spotify;

-- Here its showing some song's have 0-min durtation
-- Checking those songs

select *
from spotify
where duration_min = 0;

-- Deleting those colums's because it's showing as 0-min

delete from spotify
where duration_min = 0;

-- To check How many different types of channel we have

select distinct(channel)
from spotify;

-- To check most palyed songs (Streaming app's)

select distinct(most_played_on)
from spotify;
```

## Data Analysis

### Task-1: Retrieve the names of all tracks that have more than 1 billion streams

```sql
select track, stream
from spotify
where stream > 1000000000;
```

### Task-2: List all albums along with their respective artists

```sql
select distinct album as Album_name,artist
from spotify
order by 1;
```

### Task-3: Get the total number of comments for tracks where licensed = TRUE

```sql
select sum(comments)
from spotify
where licensed is true;
```

### Task-4: Find all tracks that belong to the album type single

```sql
select *
from spotify
where album_type = 'single';
```

### Task-5: Count the total number of tracks by each artist

```sql
select artist, count(track) as Number_of_Tracks
from spotify
group by 1
order by 1 desc;
```

### Task-6: Calculate the average danceability of tracks in each album

```sql
select album, avg(danceability) 
from spotify
group by 1
order by 2 desc;
```

### Task-7: Find the top 5 tracks with the highest energy values

```sql
select track,energy, album_type 
from spotify
order by 2 desc
limit 5;
```

### Task-8: List all tracks along with their views and likes where official_video = TRUE

```sql
select track,sum(views),sum(likes)
from spotify
where official_video is true
group by 1
order by 2 desc
limit 5;
```

### Task-9: For each album, calculate the total views of all associated tracks

```sql
select album, sum(views) as total_views,track
from spotify
group by 1,3;
```

### Task-10: Retrieve the track names that have been streamed on Spotify more than YouTube

```sql
select track
from spotify s1
where s1.most_played_on = 'Spotify'
and s1.stream > (
    select s2.stream
    from spotify s2
    where s2.most_played_on = 'YouTube'
    and s1.track = s2.track
);
```

### Task-11: Find the top 3 most-viewed tracks for each artist using window functions

```sql
with ranking_artist as (

	select artist, track, sum(views) as total_views,
	dense_rank() over (partition by artist order by sum(views) desc) as rank
	from spotify
	group by 1,2
	order by 1,3 desc
)

select *
from ranking_artist
where rank <=3;
```

### Task-12: Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album

```sql
WITH cte
AS
(SELECT 
	album,
	MAX(energy) as highest_energy,
	MIN(energy) as lowest_energery
FROM spotify
GROUP BY 1
)
SELECT 
	album,
	highest_energy - lowest_energery as energy_diff
FROM cte
ORDER BY 2 DESC;
```

## Query Optimization

```sql
select track
from spotify s1
where s1.most_played_on = 'Spotify'
and s1.stream > (
    select s2.stream
    from spotify s2
    where s2.most_played_on = 'YouTube'
    and s1.track = s2.track
);
```

### Note: Explain analyse -- Planning Time: 0.125 ms and Execution Time: 10.743 ms

![Before_Index](https://github.com/SriSurya-DA/spotify_data_exploration/blob/main/Before_index.png)

![Graphic](https://github.com/SriSurya-DA/spotify_data_exploration/blob/main/Pasted%20Graphic%201.png)

## Adding Index

```sql
create index artist_index on spotify(artist)
```

### Note: Explain analyse -- Planning Time: 0.085 ms and Execution Time: 0.052 ms

![After_Index](https://github.com/SriSurya-DA/spotify_data_exploration/blob/main/Data%20Output%20Messages%20Explain%20X%20Notifications.png)

![Graphic](https://github.com/SriSurya-DA/spotify_data_exploration/blob/main/Pasted%20Graphic%202.png)
