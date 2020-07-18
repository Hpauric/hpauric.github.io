---
layout: post
published: true
title: 'Graph Theory I&#58; Intro And Data Structure Examples'
image_url: /images/400-300px/mario-map-400.png
excerpt: Before using graph theory, you need to learn about the data structures that are used.
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

## BFS

Converting edges to adjacency lists.
We want to use adjacency lists because it makes it easy for use to access the edges for each node as we traverse the graph.
if we have the edges as an array of two element arrays, we can convert them to adjacency lists.
First create an array of linked lists and initialize them. Assuming we know the number of nodes `n`, we create an array.
```csharp
LinkedList<int>[] adj = new LinkedList<int>[n];
for (int i = 0; i < n; i++)
	adj[i] = new LinkedList<int>();
```
Each node has it's own linked list. For each edge, we add the vertex to the node's linked list. If it is an undirected graph, we also add the vertex to the other node's linked list. Assuming we know the number of edges `m`:
```csharp
for(int i = 0; i < m; i++)
{
  int u = edges[i][0];
  int v = edges[i][1];
  adj[u].AddLast(v);
  adj[v].AddLast(u);
}
```
Now we have an array of nodes we can traverse through.
We use a queue to track the nodes we want to traverse. Every time we come across a node we haven't visited, we will add it to the queue. We can stored the visited nodes as a bool array.
We'll also create an array to track the distance of each node from the start node;

```csharp
int startNode = 1;
bool[] visited = bool[n];
int[] distances = int[n];
visited[startNode] = true;
Queue<int> nodeQueue = new Queue<int>();
nodeQueue.Enqueue(startNode);
```

For each node, we'll queue up each adjacent node that hasn't already been visited, and we'll keep iterating until the queue is empty.

To iterate through the linked list, we'll access the first node, and then delete it. These operations both take `O(1)` time to complete. Accessing the `adj[node].Count` to evaluate the while loop also takes `O(1)` time. As we access each node, we'll also note that is has been visited in our `visited` array.

```csharp
while(nodeQueue.Count > 0)
{
  int node = nodeQueue.Dequeue();
  while(adj[node].Count > 0)
  {
    int nextAdjacentNode = adj[node].First.Value;
    if(!visited[nextAdjacentNode])
    {
      nodeQueue.Enqueue(nextAdjacentNode);
      distances[nextAdjacentNode] = distances[node] + 1;
      visited[nextAdjacentNode] = true;
    }
    adj[node].RemoveFirst();
  }
}
```
Now our distances array is populated with the distances of all of the nodes. The only problem is that nodes that were never visited will have a value of 0. We want to store unvisited notes with the value of -1, to differentiate them from the start node with a valid value of 0. We can do this by initializing the distances array earlier and populating it in the same iteration we use to initialize the linked list:
```csharp
LinkedList<int>[] adj = new LinkedList<int>[n];
for (int i = 0; i < n; i++)
{
  adj[i] = new LinkedList<int>();
  distances[i] = -1;
}
```