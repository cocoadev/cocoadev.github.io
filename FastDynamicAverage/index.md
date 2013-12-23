---
layout: page
title: FastDynamicAverage
---

This is probably more of a C question since "fast" and "cocoa" don't usually belong in the same sentence.  What's a very fast way to report a dynamic average.  Suppose I push a number into an object that stores a list of those numbers.  The behavior comes in one of two flavors.  The list grows until it is emptied it, or it grows until a certain size beyond which the head of the list is removed to make room for the new tail.  At any point I can call average.  There's the obvious naive way to implement this functionality, but there must be a good way to do this that would make Fibonacci proud.

----
Well, you could calculate the average each time a new number is added, store it somewhere, and return it whenever you call the average method/function. This doesn't speed up the averaging process itself, but it might "look" faster. Or you could do something like this, which is probably marginally faster than totalling and dividing each time. --JediKnil
    
static const unsigned int kMaxLength = 10; // or whatever

static unsigned int size = 0; // These should probably be ivars
static double total = 0;

- (void)addNumber:(double)num
{
   [array storeNumber:num]; // replace with your storage method
   ++size;
   if (size > kMaxLength) {
      int oldNum = [array removeAndReturnFirstNumber]; // replace with your remove method
      total -= oldNum;
      --size;
   }
   total += num;
}

- (double)average
{
    return (total / size);
}


Yeah... that's what I have... and it's brutal.  There must be a way that doesn't divide the sum each time a number is added: at the very least if the vector's length remains constant then divide before summing, such that the division operates on a smaller numbe (not sure if that helps, though)... 

How for example, does my Prius calculate the average gas mileage given that it can count up to an arbitrary number of miles?  Surely it doesn't recalculate all miles every time... does it?

----

What exactly is the problem? You want to avoid a division when calling average??? This should NOT be a problem.

What might be your problem is if     storeNumber or     removeAndReturnFirstNumber are not O(1).

Read up on TimeComplexity (and make Knuth happy) -- and also, do you want a fast     addNumber: or     average method?

----

Given N data points, their average is the sum divided by N. If you add a data point, you can calculate the new average by first recomputing the old sum (S = average * N), then adding the new data point (S = S + data), and then dividing by N + 1 (average = S / N + 1).

It sounds like the inefficiency in your code, as such, stems from the fact that you are recomputing your sum every time you add a data point, and then recomputing the average every time it is requested. Instead, every time you add a number, you'll want to cache two things: the computed average, and the number of data points used to calculate the average. Then, whenever you add a new number, you can quickly recalculate the average without adding up all the old numbers. That's probably what your Prius is doing.

The process is slightly complicated by your requirement that only the last M data points be used for the average (if I read, and you wrote, your description correctly). That is, whenever you throw away one of your previous data points, you need to recalculate your average from scratch. However, I think you could fiddle that by recomputing your sum (S = average * N), then subtracting the data point you're about to drop, THEN add your new data point, and divide by N (instead of N + 1).

----

If you're app's slow, **profile it**. Don't guess where the inefficiencies are, because you'll probably be wrong. Use one of the tools Apple provided for this happenstance, and locate the slow patch empirically. There's no other way to fly.

----
MultipleEditSyndrome! Anyway, do you ever need to access the numbers again? Do they *have* to be removed afterwards? Because if they don't, you could just keep a running total and divide by the size whenever necessary, leaving out all the number storage stuff. --JediKnil (again)

