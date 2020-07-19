---
layout: post
published: true
title: 'Graph Theory With Breadth First Search'
image_url: /images/400-300px/mario-map-400.png
excerpt: An introduction to graph data structures and breadth first search, with examples in C#
---

* ToC
{:toc}

In computer science, a graph is a collection of nodes that are connected to each other. Any network can have its relationships described by graphs. Some examples of real world scenarios that are well-represented with graphs are:
- a social network
- the london underground
- cities interconnected by roads

![mario-overworld]({{site.baseurl}}/images/graph-theory-mario.png)

## Data Structures for Graphs

There are three popular data structures used to represent graph connections. These are:
- edge lists
- adjacency matrices
- adjacency lists

As an example, we can see how these four nodes and their connections would be represented. This image was created with [VisuAlgo](https://visualgo.net/en/graphds), which is a great tool for exploring graphs and other data structures.

![VisuAlgo]({{site.baseurl}}/images/visual-algo.png)

### Edge Lists

In computer science, the technical name for a connection in a graph is an 'edge'. So an edge list is simply a list of all the connections between nodes in the graph:
```csharp
int[,] edgeList = new int[3, 2] {
{ 0, 1},   // The first edge is from Node 0 to Node 1
{ 1, 2},   // The second edge is from Node 1 to Node 2
{ 3, 4} }; // The third list is from Node 1 to Node 3
```

### Adjacency Matrices

With an adjacency matrix, every single possible connect between every node is listed. If there is no edge (connection), a `0` is used. If there is an edge, a `1` is used.

```csharp
int[,] adjMatrix = new int[4, 4] {
{ 0, 1, 0, 0}, // Node 0 is connected only to Node 1
{ 1, 0, 1, 1}, // Node 1 is connected to every other node 
{ 0, 1, 0, 0}, // Node 2 is connected only to Node 1
{ 0, 1, 0, 0}}; // Node 3 is connected only to Node 1
```
### Adjacency Lists

With adjacency lists, you *list* the edges for each node separately. You can do it as an array:
```csharp
int[][] adjList = new int[4][];
adjList[0] = new int[1] { 1 }; // Node 0 is connected only to Node 1
adjList[1] = new int[3] { 0, 2, 3 }; // Node 1 is connected to every other node 
adjList[2] = new int[1] { 1 }; // Node 2 is connected only to Node 1
adjList[3] = new int[1] { 1 }; // Node 3 is connected only to Node 1
```
However, adjacency lists are most often represented as linked lists.

## Converting an edge list to an adjacency list

Most often, we'll want to use adjacency lists because it makes it easy for us to traverse the graph - for each node we have a list of adjacent nodes at hand. It's also usually the most space-efficient way to represent a graph.
We'll use linked lists because we don't know in advance the space requirements for each adjacency list. For a graph with `n` nodes, each node could up to `n` adjacent nodes (including itself).

Assuming we know the number of nodes `n`, we declare an array of linked lists and initialize each one. 
If we have the edges as an array of two element arrays, we can convert them to adjacency lists. 
```csharp
LinkedList<int>[] adj = new LinkedList<int>[n];
for (int i = 0; i < n; i++)
  adj[i] = new LinkedList<int>();
```
Let's assume we have a edge list of length `m` stored as an array of two element arrays.
For each edge, we add the vertex to the node's linked list. If it is an undirected graph, we also add the vertex to the other node's linked list:
```csharp
for(int i = 0; i < m; i++)
{
  int u = edges[i][0];
  int v = edges[i][1];
  adj[u].AddLast(v);
  adj[v].AddLast(u);
}
```

## Breadth First Search - Calculate Shortest Path

Now we have an array of nodes we can traverse through. If we want to evaluate the distance for all nodes in the graph from a given node `startNode`, we can do this with breadth first search.
We will start by iterating through the nodes adjacent to the start node and propagate the iteration outward to all the respective adjacent nodes until all connected nodes have been processed.
Since we want to process all adjacent nodes before processing additional nodes, a queue is the best data structure to use to store and access the remaining nodes to process. Every time we come across a node we haven't visited, we will add it to the queue. 
We'll also create an array to track the distance of each node from the start node:
```csharp
int[] distances = int[n];
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
    // If the distance value for this node is -1 we know the node has not yet been visited
    if (distance[nextAdjacentNode] == -1)
    {
      nodeQueue.Enqueue(nextAdjacentNode);
      distances[nextAdjacentNode] = distances[node] + 1;
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

Here is the entire algorithm as a static method, with  parameters `n` providing number of nodes and `m` the number of edges:
```csharp
public static int[] bfs(int n, int m, int[][] edges, int startNode)
{
  int[] distances = new int[n];
  LinkedList<int>[] adj = new LinkedList<int>[n];
  for (int i = 0; i < n; i++)
  {
    adj[i] = new LinkedList<int>();
    distances[i] = -1;
  }
  for (int i = 0; i < m; i++)
  {
    adj[edges[i][0]].AddLast(edges[i][1]);
    adj[edges[i][1]].AddLast(edges[i][0]);
  }
  Queue<int> nodeQueue = new Queue<int>();
  nodeQueue.Enqueue(startNode);
  distances[startNode] = 0;
  while (nodeQueue.Count > 0)
  {
    int node = nodeQueue.Dequeue();
    while (adj[node].Count > 0)
    {
      int nextAdjacentNode = adj[node].First.Value;
      if (output[nextAdjacentNode] == -1)
      {
        nodeQueue.Enqueue(nextAdjacentNode);
        distances[nextAdjacentNode] = distances[node] + 1;
      }
      adj[node].RemoveFirst();
    }
  }
  return distances;
}
```