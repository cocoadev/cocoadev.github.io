---
layout: page
title: GraphTheoryShortestDistance
---

I wonder if somebody can help me implement an object oriented graph in Objective-C with shortest distance calculation. I've been using the example here (http://www.python.org/doc/essays/graphs.html), but both my maths and python skills are atrocious! I'm trying to map a local railway and calculate the shortest route between any given station.

STATION class

* identifier (int)
* name (string)
* connections (array of CONNECTION objects)


CONNECTION class

* connected station (int relating to a station identifier)
* time (int of travel time)


    

Cocoa Code:
- (NSMutableArray*)findPath:(NSMutableArray *)stations start:(int)startStation end:(int)endStation path:(NSMutableArray*)path {

}




Python Code:
    
def find_shortest_path(graph, start, end, path=[]):
        path += [start]
        if start == end:
            return path
        if start not in graph:
            return None
        shortest = None
        for node in graph[start]:
            if node not in path:
                newpath = find_shortest_path(graph, node, end, path)
                if newpath:
                    if not shortest or len(newpath) < len(shortest):
                        shortest = newpath
        return shortest

graph = {'A': ['B', 'C'],
             'B': ['C', 'D'],
             'C': ['D'],
             'D': ['C'],
             'E': ['F'],
             'F': ['C']}

find_shortest_path(graph, 'A', 'D')


----

I would recommend that you look at the boost library's graph component: http://boost.org/libs/graph/doc/table_of_contents.html
Not objective-C, but probably the best free implementation you're going to come across.  You can always bridge it via Obj-C++.

----

How dense is this graph?  You could probably do it using a depth-first traversal.

----

The python code that you have actually solves a different problem.  In your implementation, edges (what you call connection objects) have a weight.  Thus, you need to use a Shortest Path Algorithm.  The most straight forward algorithm to implement is Dijkstra's Algorithm ( http://en.wikipedia.org/wiki/Dijkstra%27s_algorithm ).  You will need to implement a priority queue in order to implement Dijkstra's algorithm.  You can probably find an Objective-C priority queue implementation, or you can use C++ routines ( make_heap() ).

P.S. If you are posting a homework problem to this site, shame on you.  Otherwise, I could help you out a little more.

----

One of the solutions for solving a single instance of this problem (one routing) is the A* search. http://en.wikipedia.org/wiki/A*_search_algorithm

For your first hack, depth-first if you think performance might not be a problem (will be fine if only local piece of railway)

----
For an illustration of real-time shortest distance solving, see ChemicalBurn at http://mikeash.com/?page=software/chemicalburn/index.html . The source code is available.

