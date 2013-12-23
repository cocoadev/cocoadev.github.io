---
layout: page
title: CocoaEfficiencyWithManySmallObjects
---



Hey all,
  I'm still very new to Cocoa and dont quite know all the ins & outs of making an app run efficiently so I'm wondering about some of the details.  Is there any real efficiency drain for using separate objects for very small details?  For example, just as a study I'm writing a little app that will take any given musical note, and tell you the new note if you transpose it a given number of half-steps.  For example:

The note 'A' transposed up four half-steps would be 'C#'.

So I have, as I see it two ways to go.

1. Create one object for a Musical Scale, and let musical notes be properties of that object.  Each "note" would be a two element NSArray, with element 1 being the note itself and element 2 being the octive of the note (for example C1, C2, C3, etc...).

2. Create one object for musical scales.  Create another object for musical notes.  The Scale Object would retain instances of Musical Note Objects & get the pitch & octive from the objects properties.  Essentially I'd be doing the same thing but the Note Object would manage the array as its own instance variables.

It seems like it would be more extensable and better protected to use a dedicated note object, especially if I ever wanted to add on other properties like volume or duration, but if you talk about a song that has thousands of notes any small ammount of efficiency drain could add up right?  Though in this case it might not make a whole lot of difference, it would be good information to know for the future.  Should I try to make my objects encompas as much information as I can, or is it better to break out many simple classes?  Or, for that matter, does it even matter once its compiled?

CliffPruitt

----

You have just described a textbook example of a good place to use the **flyweight design pattern**.  If you search on that you'll get the idea. (Unfortunately I don't know of a good link off the top of my head.  It's a Gang of Four design pattern.)

*C2's pattern wiki's normally a good one, though Google currently hates it. Try http://c2.com/cgi/wiki?FlyweightPattern *

*On the case at hand, why do you want an object for a Musical Scale? Why not just create a     MusicalNote class with a     -transposeBy: method? If you're worried about efficiency (and well you may be), consider using ObjCPlusPlus and making the notes CPlusPlus classes - they are generally much more efficient with space and time, as they do not by default support the incredible flexibility all ObjC classes provide. However, as a newbie, you're probably better off using a simple ObjC class for now, and rewriting it later when you feel like stretching yourself. Just be aware that there are solutions!*

**I don't know about space.. an objective-C object's space is a single pointer (isa) plus all the ivars.  Can't get much smaller than that!**

*I'm not at all sure that that's true. There's also the retain count, and I'm not sure how objects are aligned-- I used to know, but it's been a long time since I was mucking about with Apple's zoned malloc implementation. Suffice it to say that it's still small, yes, but there may be other factors.*

----

First off, I'd go with the model that suits my needs best, regardless of its efficiency, and optimize it only when it's getting too slow for my needs.

However, looking at your example, I find that the second option (i.e. dedicated object for notes) is the one that's more efficient:
The first one uses one NSArray with two elements for every note. The smallest possible array would have at least

* the reference count (4 bytes)
* the isa pointer, inherited from NSObject (4 bytes)
* the count of the array so [array count] is O(1) (I believe this is guaranteed, but I'm not sure) (4 bytes)
* a pointer to the data block of the array (or, alternatively, the data) (4 bytes, alternatively 0 bytes if the data is just concatenated at the end of the array instance)

The contents of your array would be two pointers to NSNumbers (you can't put primitives into an NSArray). That's at least another

* reference count (4 bytes)
* id (to reference the object from the array) (4 bytes)
* isa pointer (4 bytes)
* value (4 bytes)


Summed up that's at least 12+2*16=44 bytes per note. Compare this to the dedicated class:

* reference count (4 bytes)
* isa pointer (4 bytes)
* pitch (4 bytes)
* octave (4 bytes)

i.e. 16 bytes per note. Plus, you're using your own class, and can separate any methods you have for when you want to use some other tonal system.

----

If memory were a huge concern it would be best to use a CArray of integers, with the integer storing the number of semi-tones from (say) bottom-A-flat. The octave would then be     (note / 13) and the pitch     (node % 13). This kind of optimization would be best done with C++. But, as the last poster said, go with the model that suits your needs best, regardless of its efficiency! Certainly don't make your life harder by using the model I just suggested unless you find that *in the real world*, a 4-fold memory overhead is rampantly too much.

----

In answer to the first question about why I'd want a scale object instead of just transposing notes, its just part of the little study experament I'm doing to help teach myself Obj C.  The first test of the app is going to be transposing a note, but after than I'm going to see if I can figure out how to display a scale (Major, Minor, Etc...), then transpose scales, then spell the notes in a given chord for a given scale etc...  The best way I know how to do this would be to build a scale object & manipulating the notes in that object to find relative scales and chords.  Its not really anything that will be useful to me, I'm just trying to find things that are complex enough to make me work a bit but not so far outside of my ability that I cant figure them out.

Next, I'm not sure how Obj-C works as far as memory & efficiency go.  The comments above discuss allocated memory (good to know), but the other question I have is wether or not there is a performance hit with creating independant objects.  I would suppose not, as either way you have to create an NSArray object or a custom object, but lets say I was either using an int variable or a custom object that just held a single int variable.  Obviously there is going to be more memory allocated for a custom object than a single int variable, but how much of a speed hit would that really take?

I think in some ways this is just an irrelivant question cause there are very few cases that I'd ever really be using an object for somethign so small as a single int, but I'm trying to understand a little more about how cocoa works.  I'm used to interperited web scripting languages so efficiency with a compiled executable in all new territory for me.

CliffPruitt

----

My 2 pence. Don't bother thinking about efficiency of objects yet. When we are dealing with object efficiency there are two things we are usually talking about: (a) The price of creating an object, in this case allocating memory and initializing it, and (b) the price of using an object, the overhead for method calls, for instance (no pun intended).

I would only consider optimizing (a) object creation, if it meets one of two criteria: profiling shows allocation/initialization to be an unacceptable bottleneck, or deterministic time constraints are to be fulfilled. Since you are using Objective-C, I doubt the latter would apply, and since this is a study project, I doubt the former would either.
Optimizing (b) is even less necessary, as the method invocation system is generally very fast. Perhaps in a tight loop with thousands of method calls would I consider optimization.

That being said, if you would like to expand the scope of your study, and optimize for the challenge of it, the above mentioned flyweight pattern does seem to be quite applicable, and would probably be pretty fun to implement too... wow, I am such a nerd.
- JeremyJurksztowicz

*There are also ways of optimizing tight loops without sacrificing the use of ObjC. Check out OptimizingCocoaCode sometime.*

----
Yeah... I think maybe you are a nerd. :-)
Thanks for the input.  Its helpful.  Like I said, I come from a web scripting world, and my last employer insisted on ASP.  Its not the most efficient scripting envoronment in the world and efficiency of objects was always a concern.  I think I just haven't gotten my head around the idea that a compiled app runs better than an interperited web based script.

So as far as the flyweight design pattern goes, am I understnading this correctly?  It seems like its very similar to concepts used in optimizing relational database design (something I DO actually understand).  In a database, I wouldn't use a field for "state" and fill in with a bunch of instances of "FL".  I'd create one entry for each state in a "state_names" table and then let every row in other tables store references to the "FL" record in the states table.

Am I understanding that the Flyweight deal is similar, in that rather than creating 50 instances of the note "A" in octive 2, I'd just create one instance & let other objeccts retain pointers to that object?

Or am I way off? :-)

CliffPruitt

----

Yup, that's the idea.  Then you keep any extra state the notes might have externally, and pass it in when you're using the note.  You may be able to store the state more efficiently this way.  For example, you may have a state (e.g. fortissimo) that applies to a range of notes, so you can hold onto the range instead of using storage in each note.  Many of the methods on note will take a context, like this maybe:

    
-(void)drawWithFrame:(NSRect)frame context:(NSDictionary *)context;


Well, okay, maybe not draw since your note is probably a model object.   Anyway, in this way your notes are still objects, with all the goodness that entails, but can be pretty cheap to use.

----

Wow... its nice to finally understand some of this. :-)  I've gotten so sidetracked with daily workload (web scripting) and had to stop/restart learning Obj-C so many times now I was starting to wonder if I was every going to have the time to ever really get the hang of it.  Understanding this, and actually successfully re-writing one of my Applescript Studio apps in Obj-C have been a huge confidence booster.

Thanks for the help.  CliffPruitt

----

With 1000s of objects, you do not have to think about efficiency at all. You're talking about a few megabytes, less than one percent of available ram. 
Flyweight is a nice pattern, but simply not needed here.

Stephan Eggermont

----

Unless, of course, you want your code to perform well. I mean, really, "a few megabytes" is a lot of memory to keep active in the working set.

----

No, a few megabytes is not a lot of memory. As I said, less than one percent of available ram. I can iterate over 6 million objects (700MB) and do some 
lookups and calculations over them in 15 seconds. Performing well only gets an issue when you can measure it on a human scale. Everything
below 0.05 s is irrelevant. 

----
Irrelevant unless you have to do it a hundred times. I'm all for keeping the optimization gnomes away when they're not needed, but blanket statements like "Everything below 0.05 s is irrelevant" are bordering on the silly. A few megabytes could be irrelevant, or it could be crucial, it all depends on what you're doing. -- PrimeOperator

