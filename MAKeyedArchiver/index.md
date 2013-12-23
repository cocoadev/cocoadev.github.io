---
layout: page
title: MAKeyedArchiver
---

MAKeyedArchiver is a mostly drop-in replacement for NSKeyedArchiver. Keyed archiving is great, but NSKeyedArchiver is really slow for large object graphs. MAKeyedArchiver does pretty much everything NSKeyedArchiver does, but is a lot faster. It also produces smaller archives. It does not use the same file format; the APIs are compatible but the files are not. I thought others might benefit from my work. I've read CocoaDev a lot, but never posted anything, so here's some code!

You can download MAKeyedArchiver from http://www.mikeash.com/software/MAKeyedArchiver/MAKeyedArchiver.tgz

It's free to use any way you want. If you use it as part of a program, credit is nice but not required.

Details on how to use it are in the included README file. You basically have to link against libz and libcrypto, and provide one error/logging function for it to call when things go wrong. Error checking is not as good as NSKeyedArchiver, so if you're writing code for the first time that may abuse the archiver, test it with Apple's code first.

The internals are pretty ugly in places. It relies on certain things staying the same, like sizeof(int), no matter where it runs. So it'll work on the Mac, but anybody who wants to port it to, say, GNUStep will have some work. I wrote it quickly and debugged it until it worked, and it shows in places.

Anyway, I hope somebody finds this useful.

--MikeAsh

MAKeyedArchiver, like all NSCoders, suffers from recursion problems. See the section starting about halfway down the page for a discussion on what these are.

----
Hi Mike.  Do you know why NSKeyedArchiving is so slow for large object graphs, and does MAKeyedArchiver really do graphs -- are pointers to the same object encoded as pointers, or when unarchived, so I went up with a bunch of distinct instances instead of points?  Thanks for posting the source!  Good stuff!
----
Maybe someone could add some benchmarks comparing the two different archivers?

I am also curious about if it handles cycles -- also, a quick look at the source reveals that this is not endian neutral. For most Mac users this might not be a problem though.
----
From what I understand, NSKeyedArchiver is slow for two main reasons.

1) Speed is simply not a design goal, particularly archiving speed. NSCoding is really made for archiving nib files, which tend to be fairly small. They're also unarchived a zillion times more than they're archived, so Apple puts the priority on decoding speed rather than encoding speed.

2) NSKeyedArchiver has an "interesting" design decision, where it coalesces identical arrays during the archiving process. This produces smaller archives, but is a huge speed loss if you have a lot of arrays, which many of us do. Checking every array that's been encoded to see if it's identical to others that have been encoded is extremely slow in large archives.

Benchmarks would be cool. I did some basic ones during development, but did not save them. My results were that MAKeyedArchiver was about seven times faster archiving and produced files about half the size, but this will probably vary a lot depending on the size of the archive and the nature of the contents. (Lots of arrays versus no arrays, etc.)

About cycles, MAKeyedArchiver is a complete replacement for NSKeyedArchiver other than some error checking that I've left off and any details I've missed. For example, on Jaguar, classes like NSString and NSData need special handling in the archiver (thanks, Apple, for not documenting this at all) and I probably missed some more classes that need it. I may also have made mistakes in some of the more obscure coding features that my code didn't test. However, all of the goodies that everybody uses, like correct handling of cycles, conditional objects, etc. are all there.

The source code is horribly platform specific, I know. When looking at it, please keep in mind that the bulk of the code was written during a single marathon 12-hour stint, and then debugged until it worked. However, unless you're trying to use it with GNUStep or OpenStep or something, it won't be an issue. All Macs are big-endian and will be for the foreseeable future. I'm not sure how my code will fare when built in 64-bit mode for a G5, but nobody's going to be doing Cocoa programming in 64-bit mode for at least a couple of years.

If anybody would like to make the code use types with defined sizes and do endian conversions, I'd love to see it.

----
Just a quick horror story for those who want a ballpark sense of just how slow NSKeyedArchiver really is: 3.5k NSArrays containing on average 11.7 NSStrings, which in turn have an average length of 9.3, takes 17 minutes to archive on a dual 867 g4.  The final archive is about 3.5 megs, and it unarchives almost spontaneously by comparison (under 1 second).  None of the arrays are equal... and since my app runs this archiving process on quit, making the user wait about 17 minutes for the app to quit does not, in my opinion, constitute a well pollished app. :(

----
Funny thing -- I can't link against openssl -- I keep getting an undefined _md5 error.  What's going on?

*Go to /usr/lib and add libcrypto.dylib to your project, or add     -lcrypto to your Other Linker Flags in your target settings.*

----
Odd, the usual drag and drop didn't work... passing an arg directly to the linker did though... anyhow, excellent job MikeAsh.  It's less than 2 times the size (w/o compression) and about 700 times faster for the data set I have.  Still too slow though, and that may be inevitable, forcing me to rewrite the datastructure... again, though, THANK YOU SO MUCH for making your code available to the community -- this is useful.

Is your data structure complicated, or is it flat? If it's flat, you should probably see about writing a custom data format or something. If you just have a bunch of arrays of strings in a flat structure, this would probably be pretty easy to do, and I'd bet it would be quite a bit faster than a general-purpose archiver. I'm very happy to see that my code is appreciated, thanks for your comments. -- MikeAsh

The datatype I'm working with isn't all that complex, but it's not flat.  Every node is the root of two *sets* of trees.  A node can be its own child (direct or indirect) but the structure is not a graph, even if it's a bit loopy.  The number of nodes is pretty limited -- in full scale application I'm not looking at more than about 20k nodes, but the branching factor is pretty serious (x2k) and average tree depth is about 16, which makes one hell of an array-pointer-stew.  I'm not exactly sure how to encode the datatype quickly without restoring to data-dupplication...

Well, add that to the CocoaMostWanted -- I wish I could just shut off the array coalescense feature.

----

You guys really ought to report these things as performance bugs with Apple. It sounds like it could affect a lot of applications. Do you know if the problem is also present for XML serialization, cause pretty much every program use that for preferences?


----

Apple is aware of the problem. I found out about the whole array coalescing thing when I talked to an Apple engineer about why archiving was so slow. I think they're planning on turning it off, but I seem to recall that for some reason it won't happen until Tiger.

Property lists, like the stuff for preferences, are a totally different system. Plist serialization can't handle cycles, and it can only encode property list objects. If you have the same object in two different containers, it gets written out twice.

----
Well, I've been using MAKeyedArchiver for the last week or so... it beats the pants off its NS counterpart speedwise on any datatype that has any significant amount of arrays on *archive*, however it is significantly slower on *unarchive*... why I don't know, because I'm being a dumb end user... but I will be looking into the matter seriously and will report back with any solutions I may come up with... although... what's this I hear about the problem being solved in Tiger?
----
If you find out some ways to optimize the unarchiving process, I'd love to hear it. I didn't spend any time optimizing the actual code itself; the (archiving) speed gains are, apparently, all a result of my design choices.

One thing that slows down the process is a hack that's put in for compatibility with Jaguar. As I mentoined above, on Jaguar, various Apple classes use private NSCoder methods when encoded. Rather than reverse-engineer those methods, I check for those classes and encode them myself. If you don't need Jaguar compatibility, you can remove this hack and probably get a small speed boost.

You can find the relevant code starting at MAKeyedArchiver.m:334 and MAKeyedUnarchiver.m:369. To remove it, simply get rid of the if statement and everything up to and including the else. Note that archives created without this code will not be compatible with archives created with it.

----
Further use reveals that MAKeyedArchiving does not preserve the mutability of NSIndexSet instances.  An NSMutableIndexSet winds up as a plain old NSIndexSet.  I haven't looked into the cause yet.
----
**[Recursion Problems] - ** NSCoder has an inherent problem in that it must use recursion to a depth that is related to the size of your data structure. While MAKeyedArchiver avoids recursion during encoding, it is impossible for an NSCoder to avoid recursion during decoding. The reason it's "impossible" is because of     -awakeAfterUsingCoder:. Because this method can return another object, every call to     -decodeObject is forced to fully decode that object right away, and all of its subobjects. In pseudocode, you're forced to have your decode method look like this:
    
obj = [class alloc];
obj = [obj initWithCoder:self];
obj = [obj awakeAfterUsingCoder:self];
return obj;

If this method weren't there, you could write the code like this:
    
obj = [class alloc];
[self queueObjectForDecoding:obj];
return obj;

Our theoretical     -queueObjectForDecoding: method would then decode the object and set everything up. However, since the object could change, and there's no way to update all of the pointers to it, this can't be done. (Note: if classes implement     -initWithCoder: to return another object, this would cause the same problem. The docs guarantee that it returns     self, but I don't know if I really believe that.)

However, most user classes don't override     -awakeAfterUsingCoder: and they implement     -initWithCoder: to return self. *If* those  conditions can be guaranteed, it would be possible to implement a nonrecursive unarchiver. (Because of the API, a nonrecursive archiver is easy, and a nonrecursive unarchiver is hard. Weird.) You could make a new method and change your unarchiver code like so:
    
obj = [class alloc];
if([class decodingAlwaysReturnsSelf])
{
    [self queueObjectForDecoding:obj];
}
else
{
    obj = [obj initWithCoder:self];
    obj = [obj awakeAfterUsingCoder:self];
}
return obj;


This wouldn't solve the problem if you had deeply nested Cocoa collections classes or anything, but it would solve the problem for deep structures of custom classes that don't do any tricky things during initialization.

If anybody is interested in pursuing this idea, MAKeyedArchiver is probably a good place to start, as it's the only archiver out there with source other than GNUStep's archiver (as far as I know). It would take some changes to     -decodeObjectForKey: and some design changes to implement deferred decoding, but I don't think it would be a huge task. If anybody is actually interested in doing this and wants my input, post here or e-mail me. (I'd love to work on it, but I do't have the time now. Maybe if nobody's touched it in the next couple of months I'll do it. :-)

----
MAKeyedArchiving no longer works under Tiger.  A change in the way foundation class clusters are implemented breaks compatability.  Moreover, collections are no longer coalesced making archive time "normal".  From what I can tell though, NSArchiving is still very much recursive, so those of you thinking of encoding deep graphs better look elsewhere... CoreData backed by SQLite could work, though as posters have noticed, SQLite can be painfully slow if you're working with very large graphs...
----

Could you post some details, if it doesn't violate your NDA too much? I'd like to fix this problem, but don't have access to Tiger at the moment.

I'd guess that the problem is related to this code, around line 365 of MAKeyedUnarchiver.m:
    
	// this is really evil, but necessary.
	// the object must be entered in the table *before* initWithCoder is called, otherwise
	// some (all?) cycles are not detected
	// of course, we can't hang on to the temporary object, so we have to reset
	// the entry in the table after everything is done
	obj = [class alloc];
	[objectDictionary setObject:obj forKey:[NSNumber numberWithInt:objectOffset]];

The dictionary will retain (and later release)     obj before it's been initialized. Normally this isn't a big deal, but it could easily cause problems in certain situations, particularly relating to class clusters. This code has already caused some problems with certain NSData instances, so I'm not too surprised.

The proper way around this bug would be to use a dictionary which does not retain its objects so that the uninitialized object is never touched. Another possibility would be to hardcode various classes and encode them manually and totally bypass this phase for them. A similar thing is already being done for NSString and NSData (the "evil Jaguar compatibility hack") but it's doing this after the object is inserted into the dictionary.

Hopefully some details about this will allow me to fix the problem. I'm happy to hear that NSKeyedArchiver no longer suffers from speed problems, and will be glad to consign MAKeyedArchiver to the backwards-compatibility dustbin in that case. MAKeyedArchiver is recursive too, on the decode end, and this is an unavoidable "feature" of the NSCoder API. -- MikeAsh

Works for my situation as of 10.4.6.  Still a great bit of software, and soooo much better than the NS versions and a good middle ground between simple archiving and all out coredata.

