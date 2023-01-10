# Graph theory

> **Disclaimer**! The graph theory described here is simplified and does not cover the whole topic. What you can find here helps me to build up some level of understanding of graphs for processing them.

The majority of the information here comes from [The Algorithms 4th edition book](https://algs4.cs.princeton.edu/home/).

# Graph

The graph is a set of vertices and a collection of edges that each connect a pair of vertices. The screenshot below shows a simplified example of this.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672644348917/fe9fde3b-e006-48ce-9116-4faebe78edd5.png align="center")

# Definitions

When an edge connects to a vertex to itself it is a **self-loop**. When two edges connect the same set of vertices we are talking about **parallel edges**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672644822044/fee639cf-31db-4e7c-b074-64a1839c05d9.png align="center")

When an edge connects two vertices the vertices are **adjacent** to the one another and the edge is **incident** on both vertices.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672729617881/96233648-2911-43bc-8038-8355d39721c2.png align="center")

Vertices have **degree** property which indicates how many edges incident to it.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672730006295/1a62637f-abc4-4752-b194-dc29115cf915.png align="center")

The **subgraph** is the subset of edges, with their vertices, that constitutes a graph.

The **path** is a sequence of edges connected by vertices, while the **simple path** is when there are no repeated vertices in a path.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672645832770/ab4f62f3-d6e1-489d-8418-a561e6ca67ed.png align="center")

Graphs may have **cycles**, which is a special case of a path, where the path's first and last element is the same vertex.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672730737082/4e7161f9-a783-4789-91fc-0393a4aa2441.png align="center")

A graph that doesn't have a cycle is acyclic.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672731799645/7a35ba4b-aee0-4272-b4ab-aed11d424aa2.png align="center")

A path has a **length** which is the number of edges.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672645962547/9a8a6c03-e8f3-4a5c-bbfa-84d9a7afe3a2.png align="center")

A vertex is **connected** to another if there is an edge between them.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672730965845/e1b7a5f2-9432-4c21-ac4e-4da92697627c.png align="center")

## Connected graph

A graph is **connected** if there is a path to all vertices in the graph.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672731190800/3f68b6ac-054d-4435-a84e-4bbe5da5929f.png align="center")

When a graph is not connected it consists of connected components. The number of connected components also indicates the maximum of how many subgraphs that constitute the graph.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672731663047/12d796fb-1523-4363-94c0-9dbb3af7b39c.png align="center")

Trees are special graphs meaning a **tree** is an acyclic-connected graph.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672732022078/5a60aa75-53b8-4f25-8b47-a117a1bbf8e6.png align="center")

We are talking about a **forest** where we have **disjoint sets of trees**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672732306918/d6b239a1-3f20-438e-8ad0-eadb83d4eacb.png align="center")

A **spanning tree** is a subgraph of a connected graph that includes all the vertices of the graph and also a single tree. Remember, a tree is a connected-acyclic graph.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672732682343/90cdf1c4-26a1-4539-bcf4-f651028900e0.png align="center")

The **spanning forest** of a graph is the union of spanning trees of the graph's connected components.

The **bipartite graph** is a graph whose vertices and edges can be divided into subgraphs where all vertices of one subgraph are connected to the other subgraph vertices.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672733154998/6e5edac0-9f14-48b8-8f43-c489bc2f1d5e.png align="center")