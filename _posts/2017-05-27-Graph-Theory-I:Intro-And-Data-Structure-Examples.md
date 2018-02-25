---
layout: post
published: true
title: 'Graph Theory I&#58; Intro And Data Structure Examples'
image_url: /images/graph-theory-mario.png
---
Why use Graph Theory?

Here's an example from [the firehose project](http://blog.thefirehoseproject.com):

> Say you want to build a data representation of a tweet. How would you represent retweets and favorites?
**This problem sounds easy in theory**, but naturally starts moving towards **advanced graph theory searching algorithms**.  For example, for a particular tweet you may need to write a depth-first-search algorithm to determine if someone with the twitter handle “barackobama” retweeted the tweet that is supplied.

![mario-overworld]({{site.baseurl}}/images/graph-theory-mario.png)

So what exactly is graph theory? Well first of all, computer science graphs have nothing to do with your garden variety real-life graphs. These graphs are effectively ways to represent a bunch of dots connected by lines. Think of a map of cities connected by roads. Got that? Now replace "city" with "node", and "road" with "edge". Now you're talking graph language! 

The first thing you need to do to understand graph theory is use a tool to visually represent graphs, otherwise you'll go insane with confusion. My favorite tool is [VisuAlgo](https://visualgo.net/en/graphds) (see what they did there?). The runner-up prize goes to [Graph Online](http://graphonline.ru/en/).

VisuAlgo is great because you can create your own graphs and see how they are represented in the three standard varieties:

- adjacency list
- adjacency matrix
- edge list

![VisuAlgo]({{site.baseurl}}/images/visual-algo.png)

## Adjacency Lists

> Adjacency lists are most useful when we mostly want to enumerate outgoing edges of each node. This is common in search tasks, where we want to find a path from one node to another or compute the distances between pairs of nodes. 

So with adjacency lists, you *list* the edges (connections) for each node in a separate array, starting with node 0:

```javascript
var adjList = [
    [1], 		// Node 0 is only connected to Node 1
    [0, 2, 3],		// Node 1 is connected to everyone 
    			// (except itself!)
    [1],		// Node 2 is connected to Node 1
    [1],		// You get the idea	
    ];
  ```

## Adjacency Matrices

With an adjacency matrix, every single possible connect between every node is listed. If there is no edge (connection), a `0` is used. If there is an edge (connection), a `1` is used.

```javascript
var adjMatrix = [
    [0, 1, 0, 0], 	// Node 0 is only connected to Node 1
    [1, 0, 1, 1], 	// Node 1 is connected to everyone 
               		// (except itself!)
    [0, 1, 0, 0],	// Node 2 is connected to Node 1
    [0, 1, 0, 0],	// You get the idea	
    ];
```

## Edge Lists

Edge lists, as the name implies, just lists the edges (connections) between each node.

```javascript
var edgeList = [
  [0, 1]	// The first edge is from 0 to 1
  [1, 2]	// The second edge is from 1 to 2
  [1, 3]	// The third list is from 1 to 3
  ];		// And that's it! Show's over, folks.
```
You can see that the edge lists are often the most efficient way to represent a graph.


## What Next?

So now we know how to represent graphs. What do we do with them? We can use them to repesent all kinds of network relationships.

The two most popular algorithms to use with graphs are depth-first search and breadth-first search. I'm going to look at implementing a breath-first search in the next part. If you can't wait for that, you can look at [Khan Academy's excellent introduction to depth-first search](https://www.khanacademy.org/computing/computer-science/algorithms/breadth-first-search/a/breadth-first-search-and-its-uses).
