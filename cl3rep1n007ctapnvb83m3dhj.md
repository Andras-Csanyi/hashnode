## Array or LinkedList, or custom data type?

I went through the details of [Arrays](https://andrascsanyi.hashnode.dev/data-structures-array) and [LinkedLists](https://andrascsanyi.hashnode.dev/data-structures-singly-linked-list) previously and one of the significant difference between them is that inserting into the sequence is `O(n)` for array, but it is `O(1)` for linked list. I built a simple benchmark to show this difference and in the meantime I became more familiar of the `C#` implementations.

# The case
Imagine a situation where you have an array and other arrays needed to merged into it. Merge means in this case that:
- an array is inserted middle of another array
- an array is chained to another array
- an array is added before another array

The task is to create a scalable solution for this problem using either array or linked list.

# Possible solutions
- using array and add/insert the other array's elements one-by-one
- using array and add/insert the other arrays
- convert the main array to linked list and add the elements one-by-one from the other arrays
- convert the arrays to linked lists and insert/add the other linked lists
- create a custom linked list type, convert all arrays to linked lists and insert linked lists into linked lists

# Used data types
- [`Link<T>`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1?view=net-6.0) which is a variable size array in C#
- [`LinkedList<T>`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.linkedlist-1?view=net-6.0) type of C#
- [custom linked list](https://github.com/Andras-Csanyi/benchmarks/blob/d426e7a345eeec09262d810cd4866321bb19444c/array_vs_linkedlist_insert_bigo1/ArrayVsLinkedListInsertBigO1/ListVsLinkedListVsLinkedListNodeBenchmark.cs#L207) type

# Short evaluation of the solutions

## Using array and add/insert other arrays' elements one-by-one
In case of variable size list which manages the size of its internal array for us, `List<T>` provides this for us, meaning occasionally we run into a `O(n)` operation when `List<T>` increases its size. This is just the theoretical part, see for extra details below. `List<T>`s [default capacity is 4](https://source.dot.net/#System.Private.CoreLib/List.cs,23) and whenever it exceeds the capacity it [doubles the capacity size](https://source.dot.net/#System.Private.CoreLib/List.cs,421). List<T> uses [`Array.Copy()`](https://source.dot.net/#System.Private.CoreLib/src/System/Array.CoreCLR.cs,7441c6721593042c) for copying the values. So, we can consider this part of the operation as  `O(1)`.

However, the fact that [we add the items one-by-one means that this operation is `O(n)`](https://github.com/Andras-Csanyi/benchmarks/blob/d426e7a345eeec09262d810cd4866321bb19444c/array_vs_linkedlist_insert_bigo1/ArrayVsLinkedListInsertBigO1/ListVsLinkedListVsLinkedListNodeBenchmark.cs#L144). This part slows down our solution and makes it difficult to scale.

Another issue is when `List<T>` increases its internal array size. During this operation it will use minimum double size memory: the original array occupies memory, the new with increased size also occupies memory. This state stands until the copy finishes. Theoretically.

However, the [`Array.Copy()`](https://source.dot.net/#System.Private.CoreLib/src/System/Array.CoreCLR.cs,57) implementation is `unsafe` and uses `ref`s (you know the stuff they want to hide from you), so this issue is fully or almost fully mitigated.

**This solution is `O(n)`.**

## Using array and add/insert the other arrays
Compared to the previous solution it is way better as we do not iterate through the elements to be added one-by-one. Occasionally, we will see `O(n)` (theoretically) operations when `List<T>` increases its size, but the operation will be closer to `O(1)`.

For size management concerns see the previous solution.
Due to the dark magic `Array.Copy()` does this solution is not `O(n)`, it is rather closer to `O(1)`.

## Convert the main array to linked lists and add the elements one-by-one from other arrays
This solution is based on the always guaranteed `O(1)` inserts of `LinkedList<T>`. Once we have the original array converted to linked list we can insert the other arrays' elements one-by-one, iterating through them. The fact we iterate through the other arrays and the other fact that the original array must be converted to linked list (also iterating through the array) makes this operation `O(n)`. Disclaimer, I didn't use the [`LinkedList<T>.CopyTo(T[] array, int index)`](https://source.dot.net/#System.Collections/System/Collections/Generic/LinkedList.cs,191), rather I wrote a [for loop](https://github.com/Andras-Csanyi/benchmarks/blob/d426e7a345eeec09262d810cd4866321bb19444c/array_vs_linkedlist_insert_bigo1/ArrayVsLinkedListInsertBigO1/ListVsLinkedListVsLinkedListNodeBenchmark.cs#L121) and let it go. Both are `O(n)`. But, compared to the array solutions it doesn't not contain occasionally array size increase and less pressure on memory due to that it doesn't built on contagious memory.

This solution is closer to `O(n)` as only the main array has to be converted to `LinkedList<T>` (`O(n)` operation), but all the inserts are `O(1)`, however iterating through the input data is still `O(n)`.

## Convert the arrays to linked lists and add/insert them
This one sounds really good as inserting/adding linked list to linked list is `O(1)` and no array size management. However, `C#` implementation of `LinkedList<T>` [does not offer methods to chain linked lists or insert a linked list to another one](https://stackoverflow.com/questions/1094445/how-does-one-add-a-linkedlistt-to-a-linkedlistt-in-c). The only solution with provided data structures is adding elements one-by-one.

For this solution I had to create a [custom](https://github.com/Andras-Csanyi/benchmarks/blob/d426e7a345eeec09262d810cd4866321bb19444c/array_vs_linkedlist_insert_bigo1/ArrayVsLinkedListInsertBigO1/ListVsLinkedListVsLinkedListNodeBenchmark.cs#L207) linked list.

This operation is somewhere at the same place between `O(n)` and `O(1)` as the previous one since it also includes `array` --> `linked list` conversion.

# Measurements
I used [BenchmarkDotNet](https://benchmarkdotnet.org/articles/overview.html) for measuring the performance of the different solutions. This is the official tool for this in C#. [It is also used by the .NET team for benchmarking their own code](https://github.com/dotnet/performance).

I was curious how performance changes when the input elements are increased.
The changes were stored in a `Dictionary<int, int[]>` where the key is the index of the changes and it is used to determine to which index the changes should be inserted. The value contains the array should be inserted.

The length of an array should be inserted is always 100.
[How many](https://github.com/Andras-Csanyi/benchmarks/blob/d426e7a345eeec09262d810cd4866321bb19444c/array_vs_linkedlist_insert_bigo1/ArrayVsLinkedListInsertBigO1/ListVsLinkedListVsLinkedListNodeBenchmark.cs#L14) of these arrays we have was changed as you can see below.
```
[Params(100, 200, 300, 400, 500, 1000, 2000, 3000, 4000, 5000, 10000, 15000, 20000)]
```

# Results
The whole result file is available [here](https://github.com/Andras-Csanyi/benchmarks/blob/main/array_vs_linkedlist_insert_bigo1/ArrayVsLinkedListInsertBigO1/BenchmarkDotNet.Artifacts/results/ArrayListAndLinkedListBenchmarks.ListVsLinkedListVsLinkedListNodeBenchmark-report-github.md).

```
|                         Method | AmountOfChanges | SizeOfAppendedArrays | ListSize |             Mean |            Error |          StdDev |           Median |              Min |              Max |    Allocated |
|------------------------------- |---------------- |--------------------- |--------- |-----------------:|-----------------:|----------------:|-----------------:|-----------------:|-----------------:|-------------:|
| AddChangesToLinkedListOneByOne |             100 |                  100 |    10000 |         4.319 ms |         4.466 ms |       0.2448 ms |         4.320 ms |         4.074 ms |         4.563 ms |    480,003 B |
|       AddChangesToListOneByOne |             100 |                  100 |    10000 |     1,497.844 ms |     4,501.847 ms |     246.7614 ms |     1,504.771 ms |     1,247.692 ms |     1,741.069 ms |         51 B |
|       AddChangesToListAsRanges |             100 |                  100 |    10000 |       158.909 ms |       506.517 ms |      27.7639 ms |       156.082 ms |       132.667 ms |       187.978 ms |         13 B |
|    AddChangesToLLNodesAsRanges |             100 |                  100 |    10000 |         3.191 ms |         3.157 ms |       0.1730 ms |         3.244 ms |         2.998 ms |         3.331 ms |    400,013 B |
| AddChangesToLinkedListOneByOne |            1000 |                  100 |    10000 |        18.636 ms |        33.578 ms |       1.8405 ms |        18.178 ms |        17.067 ms |        20.662 ms |  4,800,026 B |
|       AddChangesToListOneByOne |            1000 |                  100 |    10000 |    15,204.707 ms |    68,839.457 ms |   3,773.3229 ms |    14,777.190 ms |    11,663.351 ms |    19,173.581 ms |        408 B |
|       AddChangesToListAsRanges |            1000 |                  100 |    10000 |     4,595.169 ms |    13,106.219 ms |     718.3961 ms |     4,622.635 ms |     3,863.434 ms |     5,299.438 ms |         27 B |
|    AddChangesToLLNodesAsRanges |            1000 |                  100 |    10000 |        11.090 ms |         9.332 ms |       0.5115 ms |        11.060 ms |        10.595 ms |        11.617 ms |  4,000,013 B |
| AddChangesToLinkedListOneByOne |           10000 |                  100 |    10000 |     1,057.627 ms |    15,838.929 ms |     868.1852 ms |       584.119 ms |       529.143 ms |     2,059.618 ms | 48,001,387 B |
|       AddChangesToListOneByOne |           10000 |                  100 |    10000 | 1,677,680.943 ms | 8,106,651.224 ms | 444,352.9083 ms | 1,670,419.527 ms | 1,237,003.244 ms | 2,125,620.059 ms | 67,109,824 B |
|       AddChangesToListAsRanges |           10000 |                  100 |    10000 |   167,542.256 ms |   962,901.976 ms |  52,779.9065 ms |   176,982.138 ms |   110,679.386 ms |   214,965.243 ms |         93 B |
|    AddChangesToLLNodesAsRanges |           10000 |                  100 |    10000 |       172.065 ms |     1,819.660 ms |      99.7417 ms |       140.335 ms |        92.049 ms |       283.812 ms | 40,000,408 B |
```

# Conclusions
The mentioned values below are `mean` values.

- merging these arrays in the way the new values are inserted one-by-one is the worst
  -  `(100) 1,497.844 ms`
  - `(1000) 15,204.707 ms`
  -  `(10 000) 1,677,680.943 ms`
  - `(20 000) 1,120,799.038 ms`
- using arrays and merging them as range is not as bad as I thought due to the dark magic (unsafe code refs) in the `Array.Copy()` implementation 
  - `(100) 158.909 ms`
  - `(1000) 4,595.169 ms`
  - `(10 000) 167,542.256 ms`
  - `(20 000) 172,870.537 ms`
-  worth to convert the arrays to linked lists and inserting the values into it, and also worth to use `LinkedList<T>` implementation 
  - `(100) 4.319 ms`
  - `(1000) 18.636 ms` 
  - `(10 000) 1,057.627 ms` 
  - `(20 000) 581.525 ms`
- in this scenario it seems doesn't worth to create a custom data type for this 
  - `(100) 3.191 ms`
  - `(1000) 11.090 ms` 
  - `(10 000) 172.065 ms` 
  - `(20 000) 663.092 ms`

## However
Originally I was planning to test only the insert for both array and linked list, but the final implementation includes transforming from array to linked list the incoming data. Which gives an additional disadvantage to the last solution. In real life situation this is the point when you have to consider how to manage this problem. You may deal with it around the insertion, meaning on the same machine or in the same process. Or you consider sourcing out this problem to another machine or service which deals with the transformation and you'll deal with only the insertion. Or, if you are lucky, you can discuss with the source team/solution to provide the data in another form than array.

The interesting thing is that the custom linked list always performs better except when `20 000` changes are inserted. This is also something worth to check why.

What is not really pushed in this test is where the new values are inserted. If you check the code you'll see that the insertion index slowly steps ahead, and when we reach the end of the array then it `break`s out from the loop. If the code has to go back and forth a lot the performance characteristics of the solutions are changing as moving to an index is `O(1)` in case of arrays and `O(n)` in case of linked lists.

So, even when you take a step away from the strictly atomic problem you'll have a lot to consider in terms of performance.

Putting this test together was a good exercise, and the next will be a test really focuses only the insert.

# Review
I would really appreciate if you share your review with me. Thank you! 
  

