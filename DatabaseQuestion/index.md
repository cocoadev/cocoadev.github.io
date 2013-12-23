---
layout: page
title: DatabaseQuestion
---

Hope someone who has experience with designing large databases will see this. I have finished a parser that can pull artist, album, song and genre information from a cddb formated album information file. I'm going to create a database for these fields for about 2 million albums. What would be the best approach for entering all of this data. Should I create individual tables for each artist? Or is there a more clever approach that will yield the quickest searches? I like the way iTunes automatically searches all fields (artist, album, song...) when you type something into the search text field. It seems like this would be a really slow search if I had to search every field of every album info. I feel like I should maybe create two tables. One table is composed of entries for each album and another table is composed of entries for each word. The first table is used to search for artists and the second table is used for a general text search. Is this a poor design?

 ----
The correct solution would be to create one table for artist, one for albums and one for songs and then join them with primary keys/foriegn keys. In this situation you would have a normalized database that is easy to insert, update and delete records to/from. To speed searches up you could de-normalize it by duplicate a field in another table, for example storing the artist's name in the album table.

If you dont't plan to update or delete records from the database once you have done the initial loading from the file there is nothing stopping you from having all information in one table and using index on some fields to speed up searches.

JoakimDanielson

----

Thanks for the input. I already started to do the last idea. I broke up artist names into a huge set of individual words. As I scanned each individual cddb album information file I parsed out the words that make up the artist's name and convert these words to all uppercase.

Every word is a key to a mutable array that stores the discid associated with each word. All of these arrays are values stored in a core foundation mutable dictionary. I then used     qsort() to sort the keys and created a lookup table based on the first two characters of every word. Then I store a word table with two fields (the word and the offset value for the storage location of the array of discid's associated with the word). 

Basically the stucture of the database looks like this:

    
[WORD INDEX TABLE] // two fields (first two characters, word table jump value)
    AA  10024
    AB  10444
    AC  10842
    ...
[/WORD INDEX TABLE]
[WORD TABLE] // two fields (word, discid table jump values)
    ABBA    304234
    ...
    BOSTON  305384
    ...
    CHEAP   308330
    ...
[/WORD TABLE]
[DISCID TABLE] // one field (discid values)
    221105F1
    0B9D22B0
    7E4A49E9
    066926E8
    100BF8FE
    71BCBBFA
    41B2F3E2
    ...
[/DISCID TABLE]


The DISCID table is a continuous array of unsinged int values (shown in hex format here). The range of discid values associated with a particular word is calculated using the discid table jump value of the word and the jump value of the next word. 

Some statics for the database created from the album information for the genre "rock"


*number of cddb album information files (each file has a unique discid value) - 392,349 albums
*size of all of these files concatenated together (this file is named     rock.cddb_cat) - 335 MB
*time to parse, sort and create database file (450MHz/768MB/80G) - ~60 sec 
*total number of words parsed (includes duplicates) - 735,052
*total number of unique words - 75,357
*database file size - 5.6 MB


    rock.cddb_cat is presorted by discid and indexed. I plan on creating similar databases for the album titles and then test to see if merging all of these databases (10 genres * 2) is worth doing. 

I haven't finished the search engine yet, so I don't have any search times. I'm still trying to decide on the criteria for search scores. This database is only for situations where internet access is not available, so I will never add new entries (that's what iTunes is for). I'll just update this database periodically when www.freedb.com has a major update.

