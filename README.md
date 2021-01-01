# data-modeling-apache-cassandra

# Purpose 
A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. 
The analysis team is particularly interested in understanding what songs users are listening to. 
Currently, there is no easy way to query the data to generate the results, since the data reside in a directory of CSV files on user activity on the app.

# Data 
We're able to create one single csv file by combining all the rows in each csv file and we have also selectively pick the columns we need.

![](images/image_event_datafile_new.jpg)

# Goal 
We need to create three tables and queries out the information we need. 

* Query 1: Give me the artist, song title and song's length in the music app history that was heard during sessionId = 338, and itemInSession = 4
* Query 2: Give me only the following: name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182
* Query 3: Give me every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'

# Query #1: 
* We need to create a table with columns (artist,song,length,session_id,item_in_session) and a partition key of session_id and a clustering column of item_in_session.
* In the first queries ask for the information where there is a particular sessionid and item in session, therefore all the songs have a particular sessionid as a partition key, 
and every time user play a new song there is a item_in_session as clustering column. 
``` 
SELECT artist,song,length from music_app_history_session
WHERE session_id = 338 and item_in_session = 4
```

# Query #2: 
* We need to create a table with columns (artist,song,userId,first_name,last_name,session_id,item_in_session) and primary key of userid and session_id and 
a clustering column of item in session. 
* First, we need the queries to order by item_in_session, there is item_in_session is a clustering column,and we have to use session_id and userid as unique key which is 
the partition key to query because like I said in the previous query,sessionid is unique to every session, and userid is also unique to every user. 
```
SELECT artist,song,first_name,last_name from music_app_history_artist
WHERE userId = 10 AND session_id = 182
```

# Query #3: 
* We need to create a table with columns (artist,song,first_name,last_name,userId) and song as the partition key and userid as the clustering column. 
* Here in this query, since we want all the user who listens to a particular song, song should be the partition key, and we also need to have userid as a 
culstering column to make sure we return all the user and does not result in "ALLOW FILTERING" warning. 
```
SELECT first_name,last_name from music_app_history_user 
WHERE song = 'All Hands Against His Own'
```
