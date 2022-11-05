---
title: 'Searching for objects in large datasets'
date: Sun, 15 Jul 2018 18:14:59 +0000
draft: false
tags: ['Groovy', 'Groovy', 'Java']
---

Groovy provides a number of useful methods for working with Lists. One that use all the time for searching for objects in a list is the `find` method. Looking under the hood you will find that is uses a linear search  via the classic  for loop to find the matching object. \[java\] public static <T> T find(T\[\] self, @ClosureParams(FirstParam.Component.class) Closure condition) { BooleanClosureWrapper bcw = new BooleanClosureWrapper(condition); for (T element : self) { if (bcw.call(element)) { return element; } } return null; } \[/java\] This gives it a time complexity of `O(n)`.

##### When the list is sorted

When you find yourself with a large sorted dataset and performance is key, the  [binary search algorithm](https://en.wikipedia.org/wiki/Binary_search_algorithm) , with a time complexity of `O(log n) `is more efficient. This is shown graphically on the complexity chart at [bigocheatsheet.com](http://bigocheatsheet.com/). Java already provides an implementation of the algorithm with the `binarySearch` method in the [Collections API](https://docs.oracle.com/javase/7/docs/api/java/util/Collections.html).

##### When you have to run the search numerous times on an unsorted list

Things get interesting when you have to run the search multiple times given an unsorted list. As I recently found out, for a data processing intensive task, it might be worthwhile to sort the list first and then perform a binary search. The Collections API provides a `sort` method implemented using the [merge sort algorithm](https://en.wikipedia.org/wiki/Merge_sort). In such a case the time complexities become:

*   `c * O(n)` - for the find method
*   `O(n log(n)) + c * O(log(n))` - for merge sort followed by binary search

You will find that if the number of iterations (c), is large enough  a merge sort + binary approach would be beneficial.