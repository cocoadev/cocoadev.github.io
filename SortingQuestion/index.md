---
layout: page
title: SortingQuestion
---

Seeing the recent posting about BubbleSort prompted me to look up some of the details of other sorting algorithms, like MergeSort and QuickSort.  In the process, however, I noticed something odd:  My Java textbook states that MergeSort is O(n*log(n)), and that QuickSort's average case is O(n*log(n)) also.  However, it goes on to say that QuickSort's worst case is O(n^2).  If this is the case, why is QuickSort considered the standard "fast" algorithm instead of MergeSort?  What makes it better? --OwenAnderson

*I am not sure it is the standard "fast" algorithm, ANSI-C does not require that qsort equals quick sort, and STL's std::sort is a hybrid of several sorting algorithms.*

*That said, quick sort is generally faster than merge sort because it does not merge the two partitions after each recursion. It does swap elements before the recursion, but this is generally cheaper, because the pivot element is in a register (and there might be better locality of reference) -- actually, with the caches we have today, I wonder if this is still true, I will make some benchmarks and return with the results later (right now I have to sleep, being on central european time).*

Also, mergesort requires additional space equal to the size of the original array, and at least one extra copy.  It's generally a constant factor slower than quicksort. *--merge sort can be iterative and inplace, so this is not necessarily true.* -- Both sorts require about the same amount of stack space (log n), 

FYI, good qsort implementations will attempt to determine if the array is already sorted or falls into some other worst-case state and use a different sort. *--this is not unique for qsort.*

One thing MergeSort has going for it is that it is a stable sort, which preserves relative order for items that match the same (like if you had a list that was sorted by first name, then you sorted it by last name, folks with the same last name would find their first names sorted correctly).  QuickSort isn't a stable sort, so the folks with the same last name would have an unpredictable ordering.  But if you don't need a stable sort, then the QuickSort variations which avoid the worst-case scenario are the way to go.

----

Another interesting approach to sorting is the set of BucketSort algorithms.  A common example is RadixSort which is O(mn) (n = size of the data set, m = log_{radix}(largest number is data set) = usually bounded by some predictable constant).

RadixSort takes advantage of the aforementioned "stability" to get essentially linear time.  As a result, it is also a stable sorting algorithm.

--JeffDisher

*RadixSort was first published 75 years ago (1929) and has been used in mechanical devices for sorting punch cards. I think it is mainly of theoretical interest today.*

*Yet another interesting approach is the counting sort, which runs in O(max(n, m)) time, where m is the highest number in the set (it only works for integers).*

http://codercorner.com/RadixSortRevisited.htm - Some more information about a modern RadixSort implementation. --OwenAnderson

----

A practical question you might want to ask for an application is whether the order produced by the sort is constant; e.g. sort by column A, then sort by column B, then sort by column A again (then C, then A, etc), is the order the same as the first time?  While quicksort is "faster" (in the elegant sense, not necessarily in user experience), you'll need something else to preserve ordering or otherwise "equivalent" elements.  Given the practically small time difference between sorting algorithms, this is often a bigger concern for the user.  Another problem with QuickSort is that it tends to do worse on partially sorted data than other algorithms (added overhead for pivot selection, or just paying the penalty for picking bad pivot), and data is very often partially sorted in practice.

----

This page http://www.cs.ubc.ca/spider/harrison/Java/sorting-demo.html has visual demonstration of a dozen different sorts. Also, I did code a quick benchmark comparing bubble sort, merge sort and std::sort -- my merge sort could be optimized, but since this thread originated from the discussion of time complexity, the most interesting thing is how the functions scale. The sort was run 1024 times on each set size, and the set was generated with rand(). The time reported is in seconds.
    
Elements  std::sort  MergeSort  Hybrid  BubbleSort
========  =========  =========  ======  ==========
       1       0.00       0.00    0.00        0.00
       2       0.00       0.00    0.00        0.00
       4       0.00       0.01    0.00        0.00
       8       0.00       0.00    0.00        0.00
      16       0.00       0.01    0.01        0.00
      32       0.00       0.01    0.01        0.01
      64       0.02       0.02    0.02        0.02
     128       0.02       0.05    0.04        0.04
     256       0.06       0.09    0.11        0.21
     512       0.12       0.19    0.18        0.79
    1024       0.25       0.41    0.38        2.99
    2048       0.53       0.75    0.76       12.14
    4096       1.17       1.92    1.79       47.38
    8192       2.44       3.95    3.78      186.15
   16384       5.40       8.50    8.02      773.35

The hybrid is MergeSort which use BubbleSort when there are
eight or less elements left to sort. I tried different values,
but 8 gave the bast result.

Btw: std::sort is already a hybrid which I believe use
InsertionSort when there are less than 20-30 elements to be
sorted.

