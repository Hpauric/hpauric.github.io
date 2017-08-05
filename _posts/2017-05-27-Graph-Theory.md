---
layout: post
published: true
---

Why use Graph Theory?

Here's an example from [the firehose project](http://blog.thefirehoseproject.com):

> Say you want to build a data representation of a tweet.  How would you represent retweets and favorites?
This problem sounds easy in theory, but naturally starts moving towards advanced graph theory searching algorithms.  For example, for a particular tweet you may need to write a depth-first-search algorithm to determine if someone with the twitter handle “barackobama” retweeted the tweet that is supplied.

So what exactly is graph theory? Well first of all, they have nothing to do with normal graphs. The best way to think of these graphs is as representations of connected dots. Think cities and roads. How do you represent these connected dots? You can do it in three ways:

- adjacency list
- adjacency matrix
- edge list

The first thing you need to do is use a tool to represent these graphs, otherwise you'll go insane.

My favorite tool is [VisuAlgo](https://visualgo.net/en/graphds) (see what they did there?). The runner-up prize goes to [Graph Online](http://graphonline.ru/en/).

VisuAlgo is great because you can see the graph and it's representation in the three main forms:

![VisuAlgo]({{site.baseurl}}/images/visual-algo.png)

## Adjacency lists

> Adjacency lists are most useful when we mostly want to enumerate outgoing edges of each node. This is common in search tasks, where we want to find a path from one node to another or compute the distances between pairs of nodes. 

So with adjacency, you list the connections for each node in a separate array, starting with node 0:

```javascript
var adjList = [
    [1], 		// Node 0 is only connected to Node 1
    [0, 2, 3], 	// Node 1 is connected to everyone (except themselves)
    [1],		// Node 2 is connected to Node 1		
    [1],		// You get the idea	
    ];
  ```
  
  Node 0 is on

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
