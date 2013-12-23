---
layout: page
title: TimeComplexity
---

*I am using my own (inconsistent) pseudo code on this page! Also, this is written as a tutorial instead of a definition.*

An algorithm is the process of mechanically applying (i.e., without a humans reasoning involved) operations to some input in order to produce output.

A simple algorithm is the identity function:     f(x) = x. It returns the element passed in (thus the identity of the element).

A more useful algorithm might be a function to search for an item in an array:

    
1.  Set value to look for
2.  Set search index to first element
3.  Check if the element at the index and the search value are "equal"
     Yes:  exit search loop
     No:   increment search index, back to step 3.
4.  Return search index.


    
linear_search ( element, array[] )
{
   i = 0;
   while( i < array.size && element != array[i] )
      i++;
   return i;
}




Unlike the identity function, this algorithm will not do the same each time, instead the time spent will depend on the input, and generally the time an algorithm takes does depend on the input. 

So in order to compare how efficient one algorithm is compared to another (e.g., binary_search vs hash_search vs plain old linear_search) we speak of the algorithms time complexity, also known as Big-O notation.

Here we count the steps in the algorithm and express it as a functionn of n, n generally being the "size" of input data, e.g. in our linear_serach, n would be the size of the array (since that is actually our input).

In our linear search the number of steps may depend on where the element is found, i.e. if it is in the beginning of the sequence, we will only need one step, if it is at the end, we will need n steps. This is the best-case and worst-case scenario, and with Big-O notation we are only interested in the worst-case scenario.

So we say that the time complexity for linear search is O(n), i.e. linear (thus the name). We may add that *it is linear in the number of elements searched* to put emphasis on the fact that n is the size of the array, but with most algorithms this is not ambiguous.

Now let us look at another search (which assume the elements are in ascending order, i.e., sorted):
    
binary_search (element, array[])
{
   first = 0, last = array.size;
   while(first != last)
   {
      middle = first + (last - first)/2;
      if(element == array[middle])
         return middle;
      else if(element < array[middle])
         last = middle;
      else
         first = middle + 1;
   }
   return array.size; // not found
}

What is the time complexity for this function? Again we are only interested in the worst-case performance.

The function consists of a loop, the range worked on in this loop is initially n, but after the first iteration, we will half this range, and in the next we will again half this new half etc. until either we find the element, or the range is zero.

So how many times can we half n? This is where the logarithmic function comes into play, cause this gives us the answer, i.e. lg(2) = 1, lg(4) = 2, lg(8) = 3 etc. (given that it is a base 2 logarithm, on most calculators it is base 10, but you can still get the result by dividing it with log(2), e.g. log(8)/log(2) = 3 no matter what the base of log is -- I generally write lg instead of log to mean base 2, some write log_2(n), where "_2" means "2 in subscript", a notation most likely made popular by General/LaTeX).

We end up with a time complexity of O(lg n).

Now we can easily compare binary_search with linear_search, since we have reduced them to two mathematical functions, and since we know that     lg n < n we know that binary_search is faster than linear_search.

This however was probably already known by the reader :) but let us take it a step further. Let us assume that we are making a database of our music collection. Items are found by artist and a GUI shows all the artists, lets the user multiselect artists and show the songs (in another view) from the selected artists.

Since our database is not sorted, we need to use linear search, and could write a functions like this:
    
find_songs (artists[], songs[], result[])
{
   for(i = 0; i < artists.size; i++)
      find_all(artists[i], songs, result);
}

find_all (artist, songs[], result[])
{
   for(i = 0; i < songs.size; i++)
   {
      if(artist == songs[i].artist)
         result.insert(songs[i]);
   }
}

We would like to know the time complexity of these new functions. The find_all resemble linear_search, but is always worst-case (since it must find all matches) and thus is still O(n). The find_songs function calls find_all m times, here m is the number of selected artists, so that results in a time complexity of O(m x n).

Since the artists come from the same database, we know that     m <= n, and in worst case there would be one song pr. artist, meaning that     m = n and give us the simpler time complexity of O(n x n), I say simpler because we only have one variable.

If you try to plot this function (see [http://www.macosxhints.com/article.php?story=20031102095312970] for how to get Calculator to plot 2D graphs), you will see that this function "explodes", e.g., assume the database contains 1,000 songs by different artists and the user selects all of them, we need to do 1,000 x 1,000 operations, which is 1,000,000 operations. Then assume he five-doubles his collection, then we get 5,000 x 5,000 operations which is 25,000,000 operations, i.e,. the number of operations required grows much faster than the number of songs, and this is generally a very serious problem for the scalability.

An alternative approach is to use the binary search from above, this was O(lg n) instead of O(n), but we would still need to execute it n times (number of selected artists), which give a time complexity of O(n lg n) -- there is a "penalty" involved, because we must ensure that the items in the database is sorted, but we can either maintain a sorted database or we can sort it before doing the query, and since sorting is O(n lg n) it will not worsen our timecomplexity.

Now try to plot     n lg n (which would be x*log(x)/log(2) in Calculator, but I could not make it work), this curve scales much better, i.e., for large values of n, the number of operations is still relatively low.

If you want to see some actual numbers for these two time complexities, look at the General/SortingQuestion page, here General/BubbleSort is O(n x n) where the two other sorts are O(n lg n), and as the input size grows, General/BubbleSort starts to take minutes to process it, where the other sorting functions are more proportional with the size of the input.

One thing that I have not mentioned are constants, e.g., if we have a function like this:
    
dummy (array[])
{
   for(i = 0; i < 5; i++)
      for(j = 0; j < array.size; j++)
         ...
}

It actually needs     3 x n operations, but we generally discard constants and say that it is O(n) instead of O(3 x n), because for high values of n it is still true that:     lg kn < ln < mn, the constants here are     k,     l and     m, and we can also calculate exactly when it is so, e.g. if one algorithm needs     16 x n operations and the other needs     n x n operations, it is easy to see that the former is to prefer when     n > 16, and when     n <= 16 the algorithm is anyway so fast that it would not matter which algorithm we choose.

The exception is when we use the algorithm (with the "high" constant) a lot, and always for a small dataset. An example here is merge sort, it split the input set in two and invokes itself recursive on each of the two new sets, afterwards it merges the two sets. This gives a time complexity of O(n lg n), which is worse than O(n x n) for     n < 8, so when the set consists of only 8 elements (or less), intead of calling itself recursively on the set, it can instead call General/BubbleSort (which is O(n x n)) on this set, and for a set of 32768 elements, we actually have 4096 invocations of merge sort with 8 elements, 8192 with 4 elements and 16384 with 2 elements, which are all eliminated, and instead we make 4096 calls to General/BubbleSort with 8 elements (which is assumed to be faster), which actually gives a rather noticable speedup.

*That is all for now -- personally I think time complexity is of major value when one wants to General/OptimizeSmart -- and even when one doesn't want to optimize, the programmer should be very intimately aware of time complexity, as at least I have seen dozen different scalability problems, all because the programmer did not use simple time complexity calculations when "designing" the "algorithms".*

Some (writers of General/OptimizeLater) thinks that time complexity is the Devil in Disguise, but it really is not -- even though you *can* hide extreme constants with the Big-O notation, I have never seen it done in any of the papers, articles or books I have read, unless it was explicitly stated (e.g., *"we have constructed an O(n) algorithm for finding the smallest enclosing circle of n points, but it is only of theoretical interest, as the constants will dominate in any practical implementation"* etc.), furthermore, the constants will be immediately visible to anyone who wants to implement the algorithm anyway, so any attempt of fooling the world to believe that you can do sorting in O(n) time, where the constant is 2^32 is doomed to fail :)

----
I'm a bit baffled by the above statement. I'm a huge advocate of General/OptimizeLater, and I do **not** feel that time complexity is the devil in disguise. 

General/OptimizeLater is a generalization, and a statement of the spirit of work. It is usually practiced in conjunction with General/TheSimplestThingThatCouldPossiblyWork and General/OnceAndOnlyOnce principles.

In other words, if I was faced with needing to sort - AND I knew the list of items to sort was unbounded ( or at least a very large boundary ), then I'd find a sort implementation for my language/platform and use it. ( General/OnceAndOnlyOnce ). If I knew the list was at most 10 items ( or even 100 if the memory requirements were small ), and none of the sorting algorithms had been implemented for my technology/situation, I'd be faced with a choice... what takes less time to implement, and is most easily maintainable? If time difference was negligible, make a smart choice and implement General/QuickSort. If time difference is significant, pick what takes the shortest time to implement ( and possibly make a comment in the code - note to tech lead/project manager/customer/documentation/insert your favorite pass-the-buck mechanism here ), that this may not be sufficient if usage assumptions were incorrect or change, and be done with it. (General/TheSimplestThingThatCouldPossiblyWork)

I realize how easy it is for someone to think that General/OptimizeLater is an absolutist statement. In practice, it is relative to the problem. More often than not, the amount of time the developer spends figuring out how to optimize a very localized problem is **much** greater than the total amount of time that optimization really saves the user. By orders of magnitude!

THAT is the problem that the General/OptimizeLater slogan is designed to address. On the flip side, when *later* becomes *now* - be polished up on all time complexity issues. Pair that knowledge up with what effects the *user* the most. Pick low hanging fruit. In business type apps, the slowest functionality is usually I/O bound. Reducing I/O with have a much bigger impact on the performance than time complexity - at first. At times the 2 problems are inter-related. And in other cases, - yes - it's all about time complexity.

In other words - a bubble sort on a set of KEYS that easily fit in memory is much more efficient than a General/QuickSort on the same set of keys paired with their values that require disk caching for each pair comparison ( with the values only 'along for the ride' ).

So identify the real sourse of the problem. That real source may or may not be time complexity alone. More acurately stated - it usually *is* time complexity, but it involves factors that were not obvious, due to encapsulated system behavior ( such as virutal memory ).

And once again - all situations/problems are not so easily categorized. General/OptimizeLater is not a mantra. It written neither in stone nor blood. It *is* an attempt to minimize sore fingers and crossed eyes, though... ;)

--General/TimHart

*Tim, the statement was not ment about the author of the page (i.e. you), but if you read the discussion, at least I find that many try to debunk time complexity as a useful tool, making it sound like one is fooled by mathematical approximations that has little to do with real-life performance, and the statement was ment for these people.*

----

-- What some people think is that undue attention is often paid to asymptotic analysis of algorithms at the expense of actual improvements to a working application.  While asymptotic analysis shows us that it is silly to implement General/BubbleSort on a large database, it tells us much less about more complex algorithms (such as algorithms for volume rendering) for which no tight bounds have been found.  The important thing to remember is that asymptotic analysis is a *guide*, not an oracle. --

A final note about time complexity: it is also useful for proving that an algorithm is optimal, for example it has been proven that you need n lg n comparisons to sort n arbitrary ordered elements which are pair-wise comparable, so there is no need to look for a faster algorithm than O(n lg n) for arbitrary input, cause it does not exist.

*Pedantic note on the above: few algorithms have proven tight bounds, the more typical case is there is a gap between the lower bound (big-Omega) and the upper bound(big-O); non-trivial asymptotic lower bounds - let alone tight bounds - for a problem (**not** an algorithm) have proven much harder to find than upper bounds.  For instance, the general search problem has a constant lower bound, but it seems unlikely anyone will come up with a non-quantum search method that's O(1), and until you do, your search can't be proven optimal.  In practical terms "optimal" generally means sub-polynomial (or polynomial or linear in some cases; depends on your field).*

I am not sure what you mean by *few* -- there should be an infinite number of algorithms and an infinite subset which have a provable lower bound :) You may refer to *algorithms from the known literate* or similar, but I was actually not. More exactly I was referring to the algorithms one constantly write during development of something, e.g. one may write an O(n^2) algorithm for HTML table code generation from a set of rectangles, and this can be proved to be optimal because n^2 table cells would arise from placing the rectangles in a diagonal etc. So it's a tool to convince oneself that it is probably pointless to spend more time on some algorithm, even though the time complexity may seem bad (but time could be spent on another higher level strategy to eliminate the algorithm).

I meant few had **proven** (not provable, but actually proven) tight bounds.  I completely agree with the utility of doing some quick analysis while programming, but convincing yourself of something is quite a different thing than proving something; hence I called it a "pedantic note".  To get a little clich�d; if I had a dollar for every time a student had a problem with something that "just can't be wrong" because they'd "proven" it... I'm sure you know the rest.
