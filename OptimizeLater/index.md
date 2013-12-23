---
layout: page
title: OptimizeLater
---

A recent thread (CreatingAClassUsingStructLikeNSPointNSRectEtc), reminded me of this concept...

http://c2.com/cgi/wiki?OptimizeLater

In addition to the arguments listed on the referred page ( both pro and con ), I've run into many, many situations where the most significant optimizations came from the most unusual places in code.

Basically - The team heeded the above advice ( for curiosity's sake ), and had been very careful to avoid PrematureOptimization. The project was not a gaming project or other resource intensive system, so proper functionality and ease of maintenance were much, much higher on the list. We were near the end of the testing and user acceptence phases, so we were quite comfortable with the relatively static state of the system.

Everyone had guesses regarding which chunk of code would be the slowest. We had even done some high level design regarding how our anticipated optimization would occur. After we ran our profiling tests, gathered our execution stacks, and execution times, we found we were all wrong. Nowhere near the mark. 3 different execution paths were common among a large portion of our most used/slowest functions. The time spent optimizing that code was relatively short. The optimization didn't result in *really* off the wall code, and because we had also adhered to the OnceAndOnlyOnce practice, the optimizations were very localized. In terms of time and effort spent optimizing, it was the easiest optimization work I had ever done, and no one on the team came close to properly identifying the bottleneck beforehand.

Having said all that, I can certainly respect the skill of those in processor intense computing that don't have the timelines or luxury to allow the computer to find the most effective optimization path.

--TimHart

----

I think the OptimizeLater technique is over-preached -- I upgraded from a 40 MHz Amiga to a 733 MHz G4 PowerMac, and seriously, some thinks are actually slower (even web-browsing was, before Safari)! I think the problem may be some combination of testing with small datasets, having fast hardware and little knowledge about time complexity.

Of course there are also less patronizing explanations, such as Quartz using floating point coordinates with transformation matrices,  double buffering everything in main memory, supporting alpha blending and extremely advanced text layout and everything with full anti aliasing...

And the high abstraction level with Cocoa also have several performance penalties (such as reloadData for a table view, rowForItem which is probably O(n) etc.).

----

There's another argument for OptimizeLater, which is that it lets you concentrate on core functionality to get your algorithms straight before you try to muck with them. Okay, it's part of Tim's argument, but I think it bears re-stating. For instance in Lodestone, which is a fairly high-overhead OpenGL program, all the geometry is calculated from instances of a general-purpose vector class.

The first thing I did was, I wrote the vector class and the abstractions used to calculate the geometry. This took me a while; I'm only an amateur mathematician (if at all--high school level meddler, really), and I have a fairly hefty block to overcome when it comes to mathematics in general, so I had to spend a fair amount of time actually figuring out the equations I'd be codifying.

After that, I realized that the drawing was pretty slow, because it was having to recalculate everything at every frame. So I used OpenGL DisplayList<nowiki/>s to speed it up; in short, I cached the geometry of elements on the canvas so that it'd only have to be recalculated when they changed.

Still, it was slow when you wanted to change the geometry of a complex element like a patch, so I rewrote LSVector as a class cluster, with subclasses LSRecursiveVector (my previous implementation) and LSFixedCubicVector, using the latter for simple cartesian {x, y, z} vectors. And you know, it's now more than fast enough.

Of course, I didn't just write the code and then optimize, not really. I wrote the code with optimizations in mind should they become necessary... and then I optimized. I believe very strongly that you'll get better performance from making solid designs with room for optimization than from optimizing at first or just coding in as simplistic way as possible and optimizing after that. Your mileage may vary, but I've seen this work.

-- RobRix

----

And perhaps that was the problem I was speaking of: if the drawing had been fast enough, would you not have spent time using display lists? cause what then, when a person with a larger object, slower bus or batteri driven machine runs your program?

I think what you did was merely prototyping, i.e. first creating the thing to get a visual result, and then doing it right.

The problem with OptimizeLater is that a) it has to be a problem for the developer and b) the design must be optimizable. And there may be many things the developer doesn't notice, I wonder if the author of Proteus knows it takes minutes to open the history, the program freezes for seconds each time the auto save kicks in etc. (because of larger history logs than what the author presumably have), iTunes which previously was completely unusable with very large mp3 collections etc. -- there are other things like the drain on battery on battery operated machines, caused by excessive polling, bad search/sorting strategies etc. etc. but this will *never* be noticed by the developer...

That said: I think the OptimizeLater preaching stems from too many people without a clue making stupid optimizations. I have once heard a guy lecture about how to optimize Java programs, and all the optimizations he proposed would have been completely implementation dependent -- some could even lead to worse performance on other JVM's.

----

I agree with Tim and Rob on this one.  I have seen too much bad code written because developers were worrying about every clock cycle.  The problem is that most optimizations only serve to complicate code which makes later maintenance and refactoring nearly impossible.  As a result, programs get bloated and slower since new code has to use ugly hacks to get around problems caused by this old "optimized" code.

However, it is probably a good idea to make code optimizable by keeping some ideas in mind while writing the code.  This, however, should be second to making sure that a design is scalable and maintainable.  Any algorithms which are using a sub-optimal computational complexity (or you think that they may need to be changed, in the future, due to changing data set sizes or constant changes) could be abstracted so that they could be re-written later.

I think that idea of OptimizeLater is something that more people should consider in usual development, however.

--JeffDisher

----

Partly that was my point: the problem is, as you state it: *developers were worrying about every clock cycle* -- so we said OptimizeLater, but this still did not solve another problem, namely that most developers do not know about TimeComplexity, and now, whenever someone with an academic background speaks about real (theoretic) optimizations, he is meet with; OptimizeLater :-/

So it is important, when we speak about optimizations, to differ between optimizations which improve the theoretic running time of the program, like using display lists in OpenGL, adding to the end of an array instead of the beginning etc. instead of those optimizations which I think you speak of, namely replacing NSArray with something (perhaps) a bit faster etc.

For the former type of optimizations you really need to plan ahead, i.e. you generally do not write a naive parser and optimize it into being O(n) or a bubble sort which through optimizations become O(n lg n)...

----

I think that many developers understand TimeComplexity.  They just seem to lose the point that TimeComplexity is fairly irrelevant when the code is run exactly once per launch (similarly for algorithms working on predictably sized data sets - this also refers to the reality that often the pure TimeComplexity is dwarfed by physical limitations of the underlying system.  ie:  bubble sort is an optimal algorithm if your data set has size relating in some special way to your cache size and memory latency) .  In such a situation, code maintainability is probably of far greater importance that the time complexity of the algorithm itself.

My point is that if you were to worry about the modularity of the design and the overall understandability and maintainability of code, you will be able to OptimizeLater within your current code-base.  This allows you to get the functional aspects of the project done faster while leaving optimizations for once you know how the software is being used and where the troublesome hot-spots are (this is a big deal with user-centric software since a developer often has no idea how someone using the software will incorporate it into their work-flow).

--JeffDisher

*Why is it irrelevant when the code is run only once pr. launch? I once used a browser which used an O(n*n) algorithm to load and parse bookmarks, and as my bookmarks grew, the startup time got worse and worse. Certainly this was irritating, even though it was only once pr. launch.*

*When is datasets of fixed size? when are they so small (i.e. 8 elements) that one should choose an algorithm with a worse time complexity?*

*Don't you think the "physical" limitations of your system may change? i.e. the first PCs did not allow for more than 640 KB of memory nor more than 32 MB harddrives -- even a year ago 2 GB was the maximum amount of memory we could place in our Macs, today it's 8 GB...*

*I really think you exaggerate in your statements, and I do not see how thinking about time complexity should interfere with program modularity/design.*

*Also, I think good programs are those which are not rushed to marked and then "improved" when they figure out that e.g. text layout is unusable with more than 100 KB's of text etc.*

----

On small data sets, the actual complexity of an algorithm can be dominated by the constant.  There was some interesting work done a few years ago by a guy that proved that bubble sort was much faster than quick sort when used on data sets which have a certain size relative to your CPU cache and memory latency.  It was to illustrate that there is a physical reality underlying the math which the raw complexity ignores.  The bubble sort algorithm, specifically, has the property of getting very high cache-hit ratios versus quick sort which tends to do quite poorly in that respect.  However, as the data set gets larger, eventually quick sort does become better, in any case.  One way or another, it is not to say that TimeComplexity may not become the purely relevant factor, later on, but that the relevant factor may not be apparent, or constant, throughout the life cycle of the program.  This is to get back to the original poster talking about how it is quite often not easy to identify the slowest parts of the code until it is complete and can be profiled.

*To prove buble sort faster than merge sort is easy: worst case buble sort needs     n*(n-1)/2 operations and merge sort needs     n*lg(n), so we just need to prove that     n*(n-1)/2 < n*lg(n), which is true when     n < 8. The guy you refer to probably *showed* that buble sort was faster for e.g. 10-30 elements, this is different, and there have been lots of similar people *showing* that one algorithm with a worse time complexity performs better for a given data set, but their "research" is not very useful, and does not in any way affect the usefulness of time complexity.*

*Quick sort is btw only *expected* to run in     O(n lg n), the worst case running time is the same as the buble sort, making this guys research even less interesting.*

Although it is obviously possible for a good algorithm to still be implemented with good code (design, in general), it has been my experience that developers tend to let design slide when they are more concerned with "efficiency."  If they stick to the design, however, it can still work.  This is best enforced by OptimizeLater.

It has been my experience that compromises made early-on in development to facilitate fast execution, result in code which is difficult to maintain.  Coming up with a good design, before hand, and shipping a product that is feature complete at least proves that the work can be done with that design.  Later optimizations can be made within the design to ensure that continued development and maintenance is not compromised.  Although it is possible to optimize as the design unfolds, doing so tends to make the code less flexible.

--JeffDisher

----

TimeComplexity generally comes with the clause: *for high values of n*. The rationale being that you generally do not care for low values of n, since even an exponential sort may perform satisfyingly when only a few values needs to be sorted. We are only concerned about how the function scales, and this should be part of the design from day one -- practical optimizations should of course not.

----

The above comment doesn't make the case strongly enough.  For low values of n, more generally-optimal algorithms can perform much worse than would be predicted.  Asymptotic analysis makes **no predictions whatsoever** about performance for low values of n; this is one of its key assumptions.  

Here's a counter-example indicating how unimportant TimeComplexity can be: standard multiplication (like you learned in school) is O(n*n), and there exists a FastFourierTransform algorithm which is O(n lg n), in both cases for n-bit numbers.  The latter algorithm is slower **in practice** when n is less than several hundred bits.  Given the standard arithmetic performed in computing is on the order of 64 bits, TimeComplexity on its own would be very misleading.

*Multiplication for 64 bit integers is O(1).*

--Not on an 8-bit machine - you may have heard of this under the alias of the "time-space trade-off".  In any case, the statement above is nothing more than a **simplifying assumption** used to facilitate the asymptotic analysis of say, matrix multiplication.  Similarly, all memory (or array, or table) accesses are not equivalent either, even though we call them all O(1).

*What I ment was, that since you fix your algorithm to work with 64 bit integers, the number of operations required is constant, thus making it O(1), i.e. no problems with scalability, which is what time complexity is really all about.*

By changing the definition of operation (in this case to mean 'bit operations', though word-level operations tend to be more important practically, such as for computing large factorizations) you arrive at entirely different conclusions (I think it should be obvious you need more than one bit-level operation to multiply two integers).  This, by the way, is exactly what I meant below about the dangers of ignoring the fundamental assumptions of asymptotic analysis: if the assumptions of the analysis aren't properly accounted for, you make practical mistakes.  This shift in the target is **very** evident when reading about practical graphics algorithms; their complexities are very often useless in comparison due to the different assumptions being made in their respective analyses.

It strikes me that it's this sort of misapplication of the technique that makes people ignore it; citing a complexity without talking about the representation of 'n', the structure of the input, and (what hasn't been mentioned yet) the space required is just thoughtless intellectualism; asserting the authority of an idea without really understanding it.--

I think the main problem with a lot of the above arguments is that they disregard most of the simplifying assumptions applied to asymptotic analysis (a.k.a. big-O notation), in particular that memory access can be performed in constant time, and that the analyses do not apply to small values of n (the precise point varying with each).

----

I think we are confusing two separate ideas: "work, work right, work fast", and "optimize for a specific situation". The first idea is about delaying optimization until you're sure your design works correctly, in all cases. This recognizes the fact that PrematureOptimization obfuscates your code, slows development time, and can actually hurt performance. The second idea recognizes the fact that the process of "optimizing" is largely about taking advantage of situation-specific knowledge to make a program run faster.

For example, the knowledge that PowerPC is relatively fast at doing floating point computation is situation-specific (CPU-specific). The knowledge that CoreGraphics is fastest at drawing certain ARGB data is situation-specific (specific to many factors). Etc.

The process of optimization is all about taking the software you have right now, and making it faster on the hardware (or other software) you have right now. You do that by making assumptions. While these assumptions are well-founded (you can measure performance, etc.) they still have limitations. The most obvious example is software Optimized for G4 will not run on G3 systems*; you make the assumption (and enforce it at runtime!) that your code will be running on a G4 processor. (*I mean at the routine level. A program can have routines that work on G3 and G4 systems, but the G4-specific routines will only run on G4/G5s.). 

So while I disagree with Rob in general, I will say I do agree OptimizeLater does not mean OptimizeNever. That's where you get into trouble. 

-- MikeTrent

*You disagree with me in general on this page or in general on anything I've said, ever? (:*

*I believe that optimizing ought to be a part of the overall design process, but that the implementation of the optimizations should (in general) wait until you've got things hammered down. I believe that PrematureOptimization is the cardinal sin of solid coding, and that everybody commits it without even realizing it. "I'll just reuse this one id ivar for six totally different processes" for instance.*

*What were we arguing about again? -- RobRix*

----

Just to add my final (hopefully) two cents:  I think that Rob's last comment sums up most of my feelings on the issue.  It is should be a part of the design but wait until "you've got things hammered down", as he so perfectly put it.

--JeffDisher

