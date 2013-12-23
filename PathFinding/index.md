---
layout: page
title: PathFinding
---

Hey, all!

I want to play around some with pathfinding in order to make my own little 'strategy' game. I have a tile-based map system and I want to know how to calculate the path from one point to another avoiding obstacles. I have tried searching pretty much on the net, but all the things I've found is above my level of knowledge. =)
So, is there any easy way to calculate how to get from 1, 1 to 9, 9 in this example, where '#' is obstacles and 'O' are "walkable"?

    
0-1-2-3-4-5-6-7-8-9
1-O.O.O.O.O.#.O.#.#
2-O.#.O.#.O.#.O.O.#
3-O.O.O.O.O.#.O.O.#
4-#.O.#.#.#.#.O.O.#
5-#.O.O.O.#.O.O.#.O
6-#.O.#.O.O.O.#.O.#
7-#.#.#.O.#.O.O.O.#
8-O.O.O.O.O.O.#.O.#
9-#.#.#.O.#.O.O.O.O



Is the 'check right' method good to use? It seems as if I'm making a large map, it could take forever getting around a little larger obstacle or an obstacle connected to one of the map-walls.


Huge thanks for all help, in advance!

--AntonKiland

----

In almost all situations, pathfinding == A* search. One decent explanation with a few links can be found at http://www.geocities.com/SiliconValley/Lakes/4929/astar.html . The gist of it is: take every path simultaneously in your head and then move based on the results of that. This is much more complicated if you don't want to let your agent know what the whole board looks like ahead of time.

I should also point out that this isn't a Cocoa question and can be answered with a quick Google search.

-- JustinAnderson

----


I wrote a simple A* pathfinding demo in Objective-C using Foundation classes (NSMutableArray, etc.). It might be a bit inefficient in a few spots but it does the job, and should be pretty easy to modify for your own needs. Source code is available here:
http://bravobug.com/news/?p=118
Cheers, -Matt


----


The Game Programming Gems books (I think the first one), has a good description of A*, along with optimizations that can be made from the base algorithm.

----

I worked it all out! It's not that hard when you finally get the hang of it. So, if anyone's wondering anything about the basics of pathfinding, you might consider asking me ;-)

--AntonKiland

----

As long as there are no islands (and even if there are in specific cases, i.e. an island that it will never get to), doing the "follow wall right" method will get you there.

- FranciscoTolmasky

