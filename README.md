# SQL_MusicStore_Analysis
SQL project to analyze online music store data

This project is for beginners and will teach you how to analyze the music playlist database. You can examine the dataset with SQL and help the store understand its business growth by answering simple questions.

## Database and Tools
* Postgre SQL
* PgAdmin4

Schema- Music Store Database  
![MusicDatabaseSchema](https://user-images.githubusercontent.com/112153548/213707717-bfc9f479-52d9-407b-99e1-e94db7ae10a3.png)

Certainly! Let’s analyze the schema of the database shown in the image. The schema represents a media store database with several interconnected tables. Here are the key points:

Tables and Relationships:
The database includes the following tables:

Artist: Contains information about artists, including their names.

Album: Stores details about albums, such as titles and artist associations.

Track: Represents individual music tracks, including attributes like name, album ID, media type, genre ID, composer, duration (in milliseconds), file size (in bytes), and unit price.

MediaType: Describes different media types (e.g., audio, video).

Genre: Contains genre information (e.g., rock, pop, jazz).

PlaylistTrack: Serves as a link between playlists and tracks, allowing multiple tracks to be associated with a playlist.

Employee: Holds employee data, including names, titles
