---
layout: page
title: ObjectsReleasedMemoryNot
---

I'm writing a Data Model for an NSOutlineView that I'm using in my app.  The NSOutlineView comes with checkboxes and I need to save their states.  Since there will be many, many items in the NSOutlineView I cannot use an individual FileSystemItem for each entry, that uses too much RAM.  So I decided to store each Pathname String together with the associated button state in a NSDictionary. Here's how I do it:

    
NSString *file;
NSString *docsDir = [NSHomeDirectory() stringByAppendingPathComponent:  @"Documents"];
NSMutableDictionary *children = [[NSMutableDictionary alloc] init];
NSDirectoryEnumerator *dirEnum =
	[[NSFileManager defaultManager] enumeratorAtPath:docsDir];

while (file = [dirEnum nextObject]) {

����[children setObject:[NSNumber numberWithInt:0] forKey:file];

}

//do something with the Dictionary, write it to disk etc.

//now I want to free up the RAM that is being used by the dictionary
//somehow I do not succeed at that
[children removeAllObjects];
[children release];
children = nil;


Now I can watch the object count go to near zero in ObjectAlloc, but my app uses just as much RAM as when I don't include
    
[children removeAllObjects];
[children release];
children = nil;


Is there something I can do so the RAM is getting released back to the OS?
It seems to me that the pathname strings provided by the NSDirectoryEnumerator are not getting released.  But I can safely assume that their are autoreleased, no? 
I do all this in awakeFromNib and my code is done after this excerpt and awakeFromNib returns, so the pathname strings from the enumerator should autorelease at that point, right?
Thanks for all the help

mOmO

----
If you're looking at the RAM usage in top or Activity Monitor, ignore it. Getting memory from the OS is expensive, and so malloc and friends will tend to keep it around as a cache rather than give it back, and then be forced to request it again when your app needs more memory. Pay attention to leaks, ObjectAlloc, and such in preference to the raw process usage figures, unless your raw figures just continue to increase forever without limit.
----

Ok thanks for the info on top and activity monitor, I didn't know that.  I was indeed checking the RAM usage of my app with activity monitor.  Like I said at the top of the page I need to keep track of a lot of files (anywhere in the hundreds of thousands) so I read in about 10000 at a time, save their button states and then write that to disk.  It is very important that my app give some of the memory back that it claims or it becomes one of the worst RAM garblers ever.  
The problem right now is that when I read in the next dictionary after I released the old one, my app is requesting just as much new RAM for the second dictionary as for the first.  So the 'caching' of RAM space that you're talking about is somehow not happening. After the second dictionary my RAM usage is up to 60MB the third 90MB the fourth 120MB, even though I properly released each dictionary after I write it to disk.  Any thoughts on this???
thanks again  
MoMo

----
The problem might be elsewhere too. You have not shown the code in between the alloc and the release. If you are using autoreleased objects in there, you might want to release them too. One thing you could do is not worry about autoreleased objects, but instead add autorelease pool around some of your blocks of code. The difference between doing this and do very careful alloc/release is probably very small in terms of performance or overall memory usage, and it is much easier to change code with autorelease pools, and less likely to leak. --CharlesParnot

----
Well the code between alloc and release is terribly simple

    
[children writeToFile:YOBACKO_HOME atomically:YES];
[children removeAllObjects];
[children release];
children = nil;

Do you think using an autoreleasepool around my code would solve my issue of my app garbling memory?
thanks
Mo

----
Well, you are tossing around a lot of autoreleased objects. Try writing your loop like this:

    
NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
while (file = [dirEnum nextObject]) {
����[children setObject:[NSNumber numberWithInt:0] forKey:file];

    [pool release];
    pool = [[NSAutoreleasePool alloc] init];
}
[pool release];


----
 --CharlesParnot YOU ARE THE MAN!!!  Seriously you know what you're doing :)
Thanks a lot, works like a charm. Exactly what I was looking for. 
Thank you thank you thank you
MoritzPfeiffer

----
Just to set the record straight: I did not write the code above, only suggested you could write something like this  but did not go as far as writing the code, because I am lazy. Somebody less lazy posted the code. So, I am just ONE OF THE MAN!!!! --CharlesParnot (and no, I don't know what I am doing)

----
Ok I see you're modest.  You did take a lot of your time,however, to help me out with my problem and that was certainly much appreciated.

----
Page orphaned, since the information that solved the problem was essentially a smart lookup. Try finding *that* info given the problem description, Spotlight!

