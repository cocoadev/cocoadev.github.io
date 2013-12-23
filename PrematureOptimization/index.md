---
layout: page
title: PrematureOptimization
---

Spending time optimizing code before it's finished is called "premature optimization". This is a sandtrap that many new programmers fall into.

Premature optimizations by new programmers frequently turn out to be pessimizations ... they decrease the performance of the code or destabilize it so that it no longer works quite right. Even if a premature optimization works correctly, keep in mind that most optimizations will tend to make the code less maintainable and more difficult to understand, modify, and debug. If you optimize before you're done, you're going to make it more difficult to finish.

In general, optimizations are best applied after you have an entire working program, so that you can find where the "hot spots" are, the things which are actually noticeably slowing down your performance. Unless you know where the hot spots are going to be, you can spend days and days making Z faster, only to find that Z is barely noticeable in your program's performance. 

Apple has provided many handy tools to help you find out where to optimize. One of the most useful is /Developer/Applications/Sampler.app. You can see the whole list at:
  http://developer.apple.com/tools/performance/

A few do's and don't's: 


* **DON'T** optimize until you can really see where your application is slow. If it's not slow, don't optimize it. (If it ain't broke...)
* **DON'T** rewrite standard library routines -- for example, stuff in Foundation or Libc. 
* **DON'T** write your own drawing code -- if drawing speed is important, look into OpenGL.
* **DON'T** worry too much about allocation of a few hundred normal objects. An NSString is cheap to create.



* **DO** improve inefficient algorithms. Learn about Big-O notation. When a better algorithm is available, use it!
* **DO** look for system services that have already implemented efficient algorithms for you. NSDictionary is a hash table, NSArray is a smart array with fairly optimal storage, the -sort routines generally perform an efficient sort, etc. * **DO** find and eliminate places where you are doing redundant work, such as sorting an array that's already sorted, reading a file more than once, or calling [array count] in the test statement of a for loop.
* **DO** try to cache and re-use expensive objects. A ten-megabyte NSData is not something you should be creating, destroying, and recreating frequently.  On the other hand, don't cache too aggressively.  If you cache big chunks of data to prevent a disk read, you may end up incurring two disk I/Os, one as it gets swapped to disk and one as it gets swapped back.
* **DO** check to make sure that your optimizations really make things faster! Keep sampling and profiling as your application grows and changes.
* **DO** check performance against all the OS versions you intend to support -- sometimes unusual differences may crop up if Apple makes something faster or slower underneath you.


-- ***from where do you have the scoop on NSArray? and wouldn't this mean less than O(1) access times for arrays? *** -- From reading the CFArray source in Darwin, which NSArray uses. And yes it does, but only for very large arrays. At fewer than 64K items, it uses a deque with O(1) access times. It's interesting reading if you can get over the ugly spacing and lack of comments!

----

In Donald Knuth's book  *Literate Programming*, he writes:
  "We should forget about small efficiencies, say about 97% of the time: premature optimization is the root of all evil."

And as William A. Wulf wrote in his famous 1972 paper, "A case against the GOTO":
  "More computing sins are committed in the name of efficiency (without necessarily achieving it) than for any other single reason - including blind stupidity."

----

My fortune output today:

*
The First Rule of Program Optimization:
        Don't do it.

The Second Rule of Program Optimization (for experts only!):
        Don't do it yet.
                -- Michael Jackson
*

I have to say that I disagree with this. It should be a standard part of the development cycle to profile a program and attempt to find sensible ways to reduce it's CPU load and memory footprint. Just because you think your program is well behaved doesn't mean that a user with limited system resources and a need to run other programs at the same time would agree with you. The exception is when you know exactly what sort of machine will be running your product, and in what environment.

Since the primary form of good optimization is to find better algorithms, I think one should be optimistic that there is always a good chance of getting a big return on a small investment in complexity. But I'm not advocating premature optimization. Just profiling and identifying relatively egregious waste, as in when one small portion of code is eating a disproportionately large amount of resources. It's up to you to define "disproportionate", and therein lies the weakness in this philosophy, but if you can reduce your system resource usage by fifty percent or thereabouts, it might make your program more popular. Set hard limits on what is a worthwhile optimization, in terms of the tradeoffs. � BrentGulanowski

----

I don't think "Don't do it" means "Don't optimize." But rather, "Find something in your program that you can avoid doing." Like, rather than look for a cheaper algorithm, look for a way to avoid parts of the work altogether and still get the same result.

In that sense, "Don't do it yet" would refer to lazy algorithms.

----

**How do you know if the optimization is premature?**


* If the optimization can just as easily take place later on in development. (The cost of the optimization does not rapidly increase).
* If optimizing will have a significant effect on the internal design of your program (limiting the flexibility, maintainability, etc.).


Please add to this list.

----

In some cases, a good alternative to PrematureOptimization is abstracting out the area which you may need to OptimizeLater (applying OnceAndOnlyOnce); therefore, the cost of optimizing will not increase further along in development - allowing you to just as easily OptimizeLater when/if it becomes necessary.

----

"They spent the whole week optimizing it. It still doesn't work, but now it doesn't work a few milliseconds faster." (from http://www.adaptiveview.com/news.html )

