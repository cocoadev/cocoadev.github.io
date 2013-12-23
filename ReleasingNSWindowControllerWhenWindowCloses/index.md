---
layout: page
title: ReleasingNSWindowControllerWhenWindowCloses
---

One and a half year of Cocoa programming and I still seem to be lost with regard to proper releasing of controllers...

I have this very simple window controller subclass:

*[...snip...]*

And     dealloc is never sent... this is a one shoot window, and I naturally would like my controller to be released (together with all top level objects of the Nib) when the window is closed.

----

Just realized the problem - some objects in the Nib bind to File's Owner, so we have sort of circular references, and thus cannot release any of the objects... the workaround would be use one or more NSObjectController instancecs in the Nib and bind to these, then setup the content objects in     windowDidLoad - not as pretty, but at least now     dealloc is again sent to my controller.

Alternatively decouple the window from the window controller by using     setWindow:nil.

----

I have never had any issues having my sub-classed NSWindowControllers releasing when I close the window. I override the     - (void) close; method like so:

    

- (void) close {
	[super close];
	[self performSelector:@selector( release ) withObject:nil afterDelay:0.];
}



(Autorelease could be used above also.) *How come autorelease works when normal releasing does not???*

Also a way to work around circular references (like with NSTimers) I do something like this:

    

- (void) release {
	if( ( [self retainCount] - 1 ) == 1 )
		[_refreshTimer invalidate];
	[super release];
}



This will let the object go through a normal deallocation cycle. But I have never had to do this with objects loaded from a NIB, since delegation objects typically don't retain the delegate object. (Unless you are doing something else.) �TimothyHatcher

----

I am, I am binding to File's Owner, and objects bound to are retained.

Each time an object (in the Nib) is bound to File's Owner, it will get an extra retain. So when releasing the File's Owner (i.e. the NSWindowController), there will still be a high retain count for each object in the Nib bound to it.


----

Binding as-in Panther's new IB feature? Or IB connections? I have no experience with the former, but if this is what happens with binds it sure presents an interesting coding conundrum. �TimothyHatcher

----

Yes, bindings as the "new" feature.

----

This is crazy. I implement windowWillClose: and send autorelease to my NSWindowController. I don't think autorelease should start dealloc when it is retained! But it doe's and this is the message I get:

An instance 0x13f03b0 of class MyObject is being deallocated while key value observers are still registered with it.  Break on _NSKVODeallocateLog to start debugging.

When I try and ordinary release nothing happens.

----

I do not know what your setup is, but I have experienced similar problems, not with autorelease, but with the fact that it is in     release of the isa-swizzled object where the check (to see if there are observers) is performed, so if the class would remove these in     dealloc it's sort of too late...

----


See: http://www.cocoabuilder.com/archive/message/cocoa/2004/6/7/109092

