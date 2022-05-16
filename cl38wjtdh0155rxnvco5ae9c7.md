## Data structures: Array

I fill up the holes is my knowledge and this is a summary of what I have learned about arrays. At first I’ll describe the data structure and I’ll take a look at the C# implementations.

## Array

Array is a data structure where items are stored in the memory and the memory addresses are known. The memory addresses can be computed either due to that the occupied memory is contiguous or the implementation built in the way it can compute the memory addresses (but still, the occupied memory is considered as one). The fact that the memory addresses of the values are known is a key factor in arrays, it provides the constant, O(1), access to elements in the array.

The size of the array is always fixed. If smaller or bigger is needed a new one has to be created and the values has to be copied.

Worth to check which language offer contiguous, and not contiguous implementation.

## Operations and Big O

```
+-----------------+-------+
|    Operation    | Big O |
+-----------------+-------+
| Add             | O(1)  |
| Delete          | O(n)  |
| Search/Traverse | O(n)  |
| Update          | O(1)  |
+-----------------+-------+
```

## Add

This operation adds a new element, as last one, to the array. The picture below shows an array (grey) which is 10 element long. The first 3 slots (red) already have value. An element is added and will occupy the fourth element in the array (yellow).

The slots in the array not filled up with value has default value, which can be `null` or something else depending on the language.

Big O for adding operation is **O(1)**.

![array-memory-canvas.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652715637191/zOBKW9CDA.png align="left")

However, the situation of adding new element to the array has to be understood clearly. If a new element is inserted as not last one rather early in the array, then all the elements after the insertion index must be shifted. The worst case is O(n) here. It might also includes that the size of the array must be increased.

## Delete

The delete operation removes the slot (value and index!) from the array and a new array going to be created with the size decreased by one and the values are copied.

Big O for delete operation is **O(n)**.

## Update

The update operation will overwrite the given element in the array with the new value. Since the memory address of the element in the array known traversing through the array is not necessary.

Big O for update operation is **O(1)**.

## Search/Traverse

In case of arrays we know the memory addresses of the elements, but we don’t know the values they have. In order to find a given value in the array we need to traverse through the array and compare every item. If we are lucky the first element is that we are looking for (O(1)), if we are not lucky it might be the last one and we have to compare all the elements in the array and the big O is linear (O(n)).

Arrays are integer indexed, so their size is integer max. (Note: I haven’t checked all the languages, but it is a good assumption.) It is important property when we are taking a look at [hash tables](https://youtu.be/hxdT_QgHUSg).

## C# implementation

C# offers two widely used, and a not recommended, implementations for arrays. The first one is the [array](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/arrays/), `int[]`, `string[]`, etc. , where the size is fixed. The array is typed (see previous sentence), or if you want to store multiple types then use `object[]`. Every array is derived from [Array](https://docs.microsoft.com/en-us/dotnet/api/system.array?view=net-6.0) which offers rich functionality to work with arrays.

If the size of the array changes then using `List<T>` is recommended. It offers size management beside the rich functionalities. It also offers that if `T` is value type the compiler creates specific implementation for the type which provides extra performance. Please note that `List<T>` uses `Array.Copy()` to manage the size of the array.

There is the third, not recommended, array implementation in C#. This is the [ArrayList](https://docs.microsoft.com/en-us/dotnet/api/system.collections.arraylist?view=net-6.0), which also provides the resizing as `List<T>` does, but it stores the data in `object[]` which brings the [boxing](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/types/boxing-and-unboxing) problem in case of value types.

When to use `[]`and `List<T>`?

- if the array size is known and not going to change during operations then `[]` seems to be a good choice
- if the array size changing then `List<T>` is a good choice

## Resources

- [Data Structures and Algorithms: The Complete Masterclass](https://learning.oreilly.com/videos/data-structures-and/9781801078504/)
- [Algorithms, Fourth Edition](https://learning.oreilly.com/library/view/algorithms-fourth-edition/9780132762564/)