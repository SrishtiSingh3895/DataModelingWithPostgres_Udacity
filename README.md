# Data Modeling With Postgres

This project was completed as a part of Udacity's Data Engineering Nanodegree Program.

## Project Description

This project aims to create a database **Sparkify** and building an ETL pipeline to create this database from the data that has been collected. The main goal of this database is to analyze this data to get information about user preferences. This was enabled by building a databse designed as a star schema with songplay table as the fact table which contains all the log data and having several dimension tables which store details of users, songs, artists and time the songs were played.

## Database Schema

A star schema based design was created for the Sparkify database to have optimized queries to get details of songs played by users. Star schema is suited for this purpose since we have a log file which contains all the metrics and then we can have details about the attributes in the fact table linked to the dimension tables.

### Fact table

#### `songplays`

This table was created using the logs that were collected when users played any song.

|*Attributes*|songplay_id|start_time|user_id|level|song_id|artist_id|session_id|location|user_agent|
|--|--|--|--|--|--|--|--|--|--|
|***Datatypes***|int|time|int|varchar|varchar|varchar|int|varchar|varchar|

Primary Key: songplay_id

### Dimension Tables

#### `users`

This table contains the details of each user.

|*Attributes*|user_id|first_name|last_name|gender|level|
|--|--|--|--|--|--|
|***Datatypes***|int|varchar|varchar|char|varchar|

Primary Key: user_id

#### `songs`

This table contains the details for each of the songs.

|*Attributes*|song_id|title|artist_id|year|duration|
|--|--|--|--|--|--|
|***Datatypes***|varchar|varchar|varchar|int|decimal|

Primary Key: song_id

#### `artists`

The table to store artist details.

|*Attributes*|artist_id|name|location|latitude|longitude|
|--|--|--|--|--|--|
|***Datatypes***|varchar|varchar|varchar|decimal|decimal|

Primary Key: artist_id

#### `time`

This table stores the start time when a song was played and all the attributes associated to that time stamp.

|*Attributes*|start_time|hour|day|week|month|year|weekday|
|--|--|--|--|--|--|--|--|
|***Datatypes***|time|int|int|int|int|int|int|

Primary Key: start_time

### Note

In all these tables, ON CONFLICT DO NOTHING constarint was implemented to avoid duplicate data and to resolve conflicts.

This database schema follows the third normal form since, all the functional dependencies X --> A have X as the super-key.

## ETL pipeline

The steps followed to build the ETL pipeline are as follows:

- Songs data files were processed to create the songs table and the artist table.
    - songs table was created by extracting the values of 'song_id', 'title', 'artist_id', 'year', 'duration' and inserting them into the table.
    - artists table was created by extracting the values of 'artist_id', 'artist_name', 'artist_location', 'artist_latitude', 'artist_longitude' and then loading them into the table.
- Log files were processed to create the users and time table.
    - The logs the in the file were filtered based on "Next Song" page click.
    - The "TimeStamp", "Hour", "Day", "WeekOfYear", "Month", "Year", "Weekday" attributes were extracted from the timestamp in the log.
    - These attributes were then loaded into the time table.
- Values from both songs data and log files were used to create the songplays table
    - The song title, artist name and duration were used to get the song_id and artist_id to be loaded into the table.
    - This was achieved by performing a join on songs and artists tables created earlier and then extracting the required information.
    - 'start_time', 'user_id', 'level', 'session_id', 'location' and 'user_agent' attributes were loaded by extracting these values from the log data.

The above steps were performed on all the files to load the entire data into the database.

## Data Analysis

The databse can be used to perform data analysis and gather information about the user preferences.

Example of few queries that can be perfoemed are:

- Analyze the number of users that are paying for the service and the number of users who are using the platform for free.
    - SELECT level, count(*) FROM users GROUP BY level;
    - This gives the output as 74 free users and 22 paid users.
- The top users, songs and artists can also be extracted using the songplays table to get the information about who are the most frequent users, what songs people play most often and the artists whom users listens to the most.
    - SELECT song_id, count(*) AS c FROM songplays GROUP BY song_id ORDER BY c LIMIT 10;
    - The above query will give the top 10 frequently played songs.
    - Similarlly, details of users, artists, etc acn also be achieved and analyzed.

## File description

The repository contains the following files/folders:

- ./data/
This folder contains all the data. Both songs and logs data are inside this folder.

- sql_queries.py
This file contains all the SQL queries required in the project including queries to drop, create, insert into, select from, the tables.

- create_tables.py
This file can be executed by giving the command "python3 create_tables.py". This will create all the tables in the Sparkify database.

- etl.ipynb
This is a ipython notebook and can be executed in jupyter notebook. This file creates the process for all the steps required later on to build the ETL pipline.

- etl.py
This file can be executed using the "python3 etl.py" command and contains the entire ETL pipline required to process, extract, transform and load the data. Executing this file will load all the data into the database tables as required.

- test.ipynb
This is a ipython notebook and can be executed in jupyter notebook. The cells of this file can be executed to verify if that data has been loaded properly into all the tables.

- README.md
This file contains the description about the project, database, databse schema and the ETL pipeline.
