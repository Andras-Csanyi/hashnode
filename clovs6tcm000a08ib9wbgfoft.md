---
title: "Sliding window"
datePublished: Sun Nov 12 2023 18:00:20 GMT+0000 (Coordinated Universal Time)
cuid: clovs6tcm000a08ib9wbgfoft
slug: sliding-window
tags: katas, interview-preparations

---

The goal of the kata is to separate the generic solution to an interview question and make brain space for the actual business logic.

**Kata**:

* write the template for a sliding window solution
    
    * pay attention to the inclusive and exclusive values, if you don't `+1` will eat your lunch
        
    * loop is 0 indexed
        
    * add an inner loop which looping depends on the values created by the outer loop
        
    * start from different values than `0`
        
    * increase the step higher than `1`
        
* explain the **O()** notation of the solution
    

**Considerations**:

* what data can be simplified?
    
* what data can be reworked to get further performance?
    
    * For example, a list looked up frequently or in every step can be reworked to HashMap for faster access.
        
* Are there partial calculations that can be re-used?
    
* Is there any opportunity to cache something?
    
* If it is possible don't do any I/O operation in the loops, if you have to emphasize its problematic nature.