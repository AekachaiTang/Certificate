# Sparkify Postgres ETL
The first project submission for the Data Engineering Nanodegree on Udacity, This project consists on putting into practice the following concepts

- Data modeling with Postgres
- Database Star schema created
- ETL pipeline using python

We are doing Data Modeling for a startup called "Sparkify". It is an online music straming platform. the startup was collecting data on songs and user activity on the streaming platform. Here Data modeling is done to empower the analytics team for getting tons of insight from the available data.

### Datasets
1. Song Dataset - Complete details and metadata about the song

2. Log Dataset - User activity log
    

##### Fact Table
Fact table is going to be "songplays" table which contains the metadata of the complete information about each user activity. Many dimension tables are connected to one fact table.

**songplays** - records in log data associated with song plays i.e. records with page NextSong
- songplay_id (INT) PRIMARY KEY: ID of each user song play 
- start_time (DATE) NOT NULL: Timestamp of beggining of user activity
- user_id (INT) NOT NULL: ID of user
- level (TEXT): User level {free | paid}
- song_id (TEXT) NOT NULL: ID of Song played
- artist_id (TEXT) NOT NULL: ID of Artist of the song played
- session_id (INT): ID of the user Session 
- location (TEXT): User location 
- user_agent (TEXT): Agent used by user to access Sparkify platform


#### Dimension Tables

**users** - users in the app
- user_id (INT) PRIMARY KEY: ID of user
- first_name (TEXT) NOT NULL: Name of user
- last_name (TEXT) NOT NULL: Last Name of user
- gender (TEXT): Gender of user {M | F}
- level (TEXT): User level {free | paid}

**songs** - songs in music database
- song_id (TEXT) PRIMARY KEY: ID of Song
- title (TEXT) NOT NULL: Title of Song
- artist_id (TEXT) NOT NULL: ID of song Artist
- year (INT): Year of song release
- duration (FLOAT) NOT NULL: Song duration in milliseconds

**artists** - artists in music database
- artist_id (TEXT) PRIMARY KEY: ID of Artist
- name (TEXT) NOT NULL: Name of Artist
- location (TEXT): Name of Artist city
- lattitude (FLOAT): Lattitude location of artist
- longitude (FLOAT): Longitude location of artist

**time** - timestamps of records in songplays broken down into specific units
- start_time (DATE) PRIMARY KEY: Timestamp of row
- hour (INT): Hour associated to start_time
- day (INT): Day associated to start_time
- week (INT): Week of year associated to start_time
- month (INT): Month associated to start_time 
- year (INT): Year associated to start_time
- weekday (TEXT): Name of week day associated to start_time

## Project structure

Files used on the project:
1. **data** folder nested at the home of the project, where all needed jsons reside.
2. **sql_queries.py** contains all your sql queries, and is imported into the files bellow.
3. **create_tables.py** drops and creates tables. You run this file to reset your tables before each time you run your ETL scripts.
4. **test.ipynb** displays the first few rows of each table to let you check your database.
5. **etl.ipynb** reads and processes a single file from song_data and log_data and loads the data into your tables. 
6. **etl.py** reads and processes files from song_data and log_data and loads them into your tables. 
7. **README.md** current file, provides discussion on my project.

### Break down of steps followed

1. Wrote DROP, CREATE and INSERT query statements in sql_queries.py

2. Run in console
 ```
python create_tables.py
```

3. Used test.ipynb Jupyter Notebook to interactively verify that all tables were created correctly.

4. Followed the instructions and completed etl.ipynb Notebook to create the blueprint of the pipeline to process and insert all data into the tables.

5. Once verified that base steps were correct by checking with test.ipynb, filled in etl.py program.

6. Run etl in console, and verify results:
 ```
python etl.py
```

## ETL pipeline

Prerequisites: 
- Database and tables created

1. On the etl.py we start our program by connecting to the sparkify database, and begin by processing all songs related data.

2. We walk through the tree files under /data/song_data, and for each json file encountered we send the file to a function called process_song_file.

3. Here we load the file as a dataframe using a pandas function called read_json().

4. For each row in the dataframe we select the fields we are interested in:
    
    ```
    song_data = [song_id, title, artist_id, year, duration]
    ```
    ```
     artist_data = [artist_id, artist_name, artist_location, artist_longitude, artist_latitude]
    ```
5. And finally we insert this data into their respective databases.

6. Once all files from song_data are read and processed, we move on processing log_data.

7. We repeat step 2, but this time we send our files to function process_log_file.

8. We load our data as a dataframe same way as with songs data. 

9. We select rows where page = 'NextSong' only

10. We convert ts column where we have our start_time as timestamp in millisencs to datetime format. We obtain the parameters we need from this date (day, hour, week, etc), and insert everythin into our time dimentional table.

11. Next we load user data into our user table

12. Finally we lookup song and artist id from their tables by song name, artist name and song duration that we have on our song play data. The query used is the following:
    ```
    song_select = ("""
        SELECT song_id, artists.artist_id
        FROM songs JOIN artists ON songs.artist_id = artists.artist_id
        WHERE songs.title = %s
        AND artists.name = %s
        AND songs.duration = %s
    """)
    ```

13. The last step is inserting everything we need into our songplay fact table.

