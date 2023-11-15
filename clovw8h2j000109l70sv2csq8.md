---
title: "Graph traversal"
datePublished: Sun Nov 12 2023 19:53:36 GMT+0000 (Coordinated Universal Time)
cuid: clovw8h2j000109l70sv2csq8
slug: graph-traversal
tags: katas, interview-questions, interview-katas

---

The goal of the kata is to separate the generic solution to an interview question and make brain space for the actual business logic.

**Kata**:

* write a code processes graph and tree
    
    * recursion
        
    * backtracking
        
* outputs the path
    
* uses BFS and DFS
    
* look for "is path exist", "how many path exists" and "shortest path" solutions
    
* **Testing**:
    
    * use a tree as input
        
    * use a graph as input
        
        * simple
            
        * complex where vertices connect to multiple other vertices
            
        * there is a loop in the graph
            
* Explain the O() category of graph processing
    

**Considerations**:

* if operations happen at every recursion consider:
    
    * what can be reused?
        
    * can look up data converted to a faster access format like HashMap?
        
* what might be the impact of graph processing on the stack? what solution do you recommend and why?