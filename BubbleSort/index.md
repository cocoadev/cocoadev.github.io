---
layout: page
title: BubbleSort
---

 is regarded around the computer science world as one of the slowest, most work intensive sorting algorithms around. It's almost optimally bad. Anyone using C or C-based programming languages has immediate access to better sorting functions (qsort() for instance). There is no situation where using BubbleSort is a good idea.

-- MikeTrent

*Was it invented as a clever benchmark? E.g. such-and-such an algorithm performs 8000% better than BubbleSort on such-and-such a dataset?*

No, I'm pretty sure it was invented just because it's the most obvious method of sorting.  It doesn't involve any fancy algorithms or anything.  It's just two nested loops through the array, comparing all the elements.  Unfortunately, while this is the most obvious solution, it is also (generally speaking) the worst, scaling at O(n^2). --OwenAnderson

And it got the name because it will take one element at a time, and swap with the next one until it find its place, thus moving a "bubble" up through the array (or a wave).

There is a similar sort:  Brick Sort.  A Brick sort moves elements downward (sink like a brick).  Many Bubble sorts are really Brick sorts.

----
Maybe that's backwards... a BubbleSort is only a Brick Sort through rose colored classes.... :P

----

For small lists to be sorted, BubbleSort can beat out other sort algorithms because it has no startup cost and very low per-iteration cost.  It just does a whole freaking lot of iterations for large lists.  Some sorting optimzations are to use the fancier algorithms to get the lists to be sorted into small enough chunks, and then BubbleSort those chunks.

*That's right: always remember that O(n^2) implies a sufficiently large n - for small problems, asymptotic analysis is not an effective guideline, since the algorithm will be dominated by constant factors (which aren't always constant; e.g. string comparison).*

----

Although the BubbleSort is a slow way of sorting things (in general), there is something to be said for the fact that it can "end early". (That is, if a BubbleSort iterates through a list once without sorting anything, it knows for certain that the list is already sorted. Using some other sorts, however, you can't be sure that a list is sorted until every single iteration has been completed.) That, I think, is BubbleSort's only redeeming quality.

*As the numbers show at SortingQuestion: already with a thousand elements, BubbleSort is ten times slower than a rather naive implementation of MergeSort, and from there it just gets worse and worse, it practically explodes with respect to time consumption (as the TimeComplexity has already warned us about), so it should never ever be used for anything besides a fixed number of elements, and that number should probably not be more than a hundred (even with 32 elements it is slower than std::sort, which is IntroSort, a hybrid based on QuickSort).*

