---
layout: page
title: ForLoopThread
---

Now that we are getting 'clovertown' and with it threads more and more a must, can someone come up with an object (NSForLoop  ? or ThreadEnumerator that directs (f.e. the outer for loop) in threads? Until now threads are nice, but far from simple.  PaulC.

----

Creating threads (and, by extension, caching and reusing them efficiently) is not the hard part, it is synchronization and, more fundamentally, division of workloads into parallelizable work units.  The outer for loop is too much of a C-level construct and too specific to a different kind of task (ie:  iterative tasks are generally not parallelizable across iterations since iteration n+1 frequently depends on iteration n's work - this is the fundamental problem with vectorization, as well).

One could create an abstraction of a discrete work unit and a thread pool runtime sort of construct within which the work units could be scheduled but this is far from the problem domain of most software (it is interesting for self-contained, compute-intensive problem domains, though).

Most applications are too largely sequential so they are hard to fit into this model.  What problem are you actually looking to solve?  There may be a way to benefit from parallel execution on a high level but there is not yet a silver bullet for such things (and there may never be).

--JeffDisher

----

I'm a firm believer in the theory that you shouldn't even contemplate writing a general-purpose library for something until you've already solved it in a working application at least once and preferably twice. So if you're thinking about making such a library, make sure that you've already written an app that would need it (and that you had to write your own multithreading code for) before you start in on the library, otherwise you're likely to end up with one that doesn't really fit what's needed.

In the end, I'm not sure if things are to the point yet where a single multithreading library will work for a large number of apps. The field is still too varied and there aren't any really standardized idioms. -- MikeAsh

----

Hear, Hear! However, consider this:

    
void myThread (void * data)
{
    assert(data);
    thread_status * stat = reinterpret_cast<thread_status*>(data);
    while(!stat->should_stop_thread() && MY_THREAD_CONDITIONS)
    {
        stat->did_iterate();
        // Do work
    }
}


Pardon the C++ (dirty word around here), but you get the gist. Now if some other part of the app wanted to, say, stop the thread cleanly, then *thread->stop()* might be useful. Or if you wanted to know how much work you have done you might ask *(thread->iteration_count()*work_per_iter)/total_work* or some such thing. I have had to service these types of designs many times, and have exported some code to a generalized C++ class. Something along these lines in Cocoa should not be too hard. That is my impression of the OP's question. That being said, this is a *personal pattern*, not a generalized pattern. That is, I am familiar with designs using this pattern and can spot an appropriate place to use it by intuition (and further research). I second the above posters sentiments when I say try solving YOUR threading problem first, general idioms are a rarity in this domain, and stumbling blocks are common. --JeremyJurksztowicz

----
I have a similar thing that I turned into an internal-use library, after I *had* solved the problem a couple of times previously. It's useful, but weird enough that I'm not sure it would be useful for situations where I wasn't around to tweak or hold hands. In any case, some very basic things like you illustrate could very well be useful if turned into a decent library. -- MikeAsh

