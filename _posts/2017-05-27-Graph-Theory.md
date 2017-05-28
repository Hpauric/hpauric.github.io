---
layout: post
published: true
---

> There are generally two standard representations of graphs that are used in graph algorithms, depending on which question is more important.

## Adjacency lists

> Adjacency lists are most useful when we mostly want to enumerate outgoing edges of each node. This is common in search tasks, where we want to find a path from one node to another or compute the distances between pairs of nodes. 

```javascript
var adjList = [
    [1],
    [0, 4, 5],
    [3, 4, 5],
    [2, 6],
    [1, 2],
    [1, 2, 6],
    [3, 5],
    []
    ];
  ```

## Adjacency Matrices

## Path Searches

The two most popular algorithms are depth-first search and breadth-first search.

## Resources


https://visualgo.net/en/graphds

http://graphonline.ru/en/


Code from Khan Academy on Breadth-First Search

// Traverse the graph
while (!queue.isEmpty()) {
    var  = queue.dequeue();

    for (; ; ) {
	var  = graph[][];

	if (bfsInfo[]. === ) {
	    ;
	    ;
	    ;
	}
    }
}
