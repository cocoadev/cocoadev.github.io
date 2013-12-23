---
layout: page
title: BetterApproachForStoringPixelsInADatabase
---



I have potentially large 2-D arrays of integer values with eac value representing a pixel on a grid, large being defined as processing 3+ arrays of int grid[30000][30000] integers each. These matrices represent a map surface. I would like to store this information inside a database for various reasons. 

My question is what would be the best approach? Does anybody have any experience or advice? Does anybody know a good reference for this problem?

So far I have found references to three potential methods of storage.

1) Each array is a column with each pixel on a row:
    
x | y | map1 | map2 | ...|
-------------------------|
0 | 0 | 1000 |   1  | ...|
0 | 0 | 1000 |   1  | ...|
0 | 0 | 1100 |   4  | ...|
. | . |    . |   .  | ...|
. | . |    . |   .  | ...|
. | . |    . |   .  | ...|

Benefits: Maps straight to arrays for which my analysis algorithms are already written.

Deficiencies: Very large storage overhead. Can't take advantage of compression techniques.


2) Each array is stored as binary data such that an array is contained in one column and row (shown as commas separated values for ease of reading):
    
name | values
-------------
map1 | 1000, 1000, 1100, ....
map2 | 1, 1, 4, ....


Benefits: When I need the data, I just pull the whole array and do something with it. Can take advantage of compression techniques such as run length encoding.

Deficiencies: Doesn't use the database paging system and large maps could cause disk thrashing (is this true, or would the database keep the binary data on disk and page in/out as needed?). If data is compressed, would have to do a whole array search to find a value at a specific [x][y] location.


3) Store each map in a column, but each row is a map row:
    
row | map1                  | map2 | ...
----------------------------------------------------
  0 | 1000, 1000, 1100, ....|   1, 1, 4, ....  | ...
  1 | 1300, 1350, 1300, ....|   2, 1, 1, ....  | ...
  . |    .                  |   .              | ...
  . |    .                  |   .              | ...
  . |    .                  |   .              | ...


Benefits: Compromise between the two techniques. Can retrieve into memory only the rows I need at the present moment, taking advantage of database paging by row. Can compress each row and searching could go directly to a specific row to find a value at a specific x (column) location, thus not requiring a whole array search.

Deficiencies: If I pull up a row, decompress it for processing, release it, then call it back into memory later I would need to decompress it again. This would be a problem since some of my analysis algorithms scan the map multiple times. 


Any input would be much appreciated!

----
For the last solution, you could try adding a cache, which either stores the last few accessed rows, or just contains all the rows that have been accessed. Then, when your application quits, you can write the cache back into memory. --JediKnil

----
I was thinking the same thing, but have a cache that would store *all* the uncompressed rows, until it filled up (give it some maxiumum size, at which point it starts acting like a LIFO queue.  It seems like there should be some nifty data structure out there to do this.. 

----
It seems like both answers are prefering option 3. If the majority of analyses proceed through the array row-by-row, wouldn't a FIFO queue be a better way? I have data structures for both in by "Algorithms in C" book by Sedgewick. The cache system seems to make the most sense for searching by row/column. I would appreciate any more input. In the mean time I will look at both LIFO and FIFO structures and see how I might implement it inside Cocoa for a test. For now I think I will ignore compression/decompression and concentrate on how well the cache works. 

On a side note, since this is geographic data and I am most often interested in grid locations next to each other, tiling would be a better way to page blocks instead of rows. I will also look at using this method where each database row is a block.

