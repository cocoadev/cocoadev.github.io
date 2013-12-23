---
layout: page
title: RadixSort
---

Radix Sort is a type of BucketSort which is often used to sort tables of integers.

(this description is idealized to assume only non-negative integers but can be applied to all integers)

The algorithm is something like this (in pseudocode):

    

sort(inputArray, radix, largestNumber)
 numIterations = floor(log_{radix}largestNumber)
 for (x =0 to numIterations)
  foreach y in inputArray
   tempArray[ floor(y/(x*radix)) modulo radix ]->append(y)
  intputArray = concatenate(tempArray)
 return inputArray



The algorithm has TimeComplexity O(mn) where n = size of the inputArray and m = floor(log_{radix}largestNumber).

With a known largest value in the input set, this algorithm approximates O(n).

Radix Sort is stable.

--JeffDisher

A Polish friend of mine was attempting to make me use this. I don't recall it being appropriate for the task at hand, though. Definitely a clever algorithm.

----

http://codercorner.com/RadixSortRevisited.htm

While looking for more information about RadixSort, I came across this site which discusses ways of making a more modern RadixSort implementation.  It adds such features as float sorting, negative integer sorting, negative float sorting, and temporal coherence.  It also uses some neat tricks to avoid scanning any radices that have no useful information, which ensures the minimum constant. --OwenAnderson

