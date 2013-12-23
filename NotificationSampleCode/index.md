---
layout: page
title: NotificationSampleCode
---

I'll throw in an example that I use to do asynch notification from a spawned worker thread.

The background is this.  In my finder replacement, I need to fork a thread to get the size of a bundle.  I don't want the user interface to freeze while I go off doing a recursive descent of the bundle hierarchy, so I fork a thread to do the work, and use Notification to tell my controller when it finished so the view can be updated.  (You see Finder doing this when you go into /Applications, all the sizes are listed as "--", and then they fill in asynchronously.)

If I find any bundles in a directory, I fork the thread
    
	if (needsUpdate)
		[NSThread detachNewThreadSelector: @selector(updateBundles)
			toTarget: self withObject: nil];


Then updateBundles runs in its own thread, so I have to use my own autorelease pool so that my objects don't leak.  When I am done doing the slow operation, I post a notification.  Note that the object: self is important; that is saying this notification is for THIS instance of my directory object only.  Listeners who want to receive these notifications need to have the same instance in their object: parameter. (see below).

    
- (void)updateBundles
{
	// this is run in its own thread, so it needs its own autorelease pool.
	NSAutoreleasePool *pool =[[NSAutoreleasePool alloc] init];

	// do all the time consuming work here...

	[pool release];
	[[NSNotificationCenter defaultCenter] 
		postNotificationName: @"DirChanged" object: self];
}


**[ MikeTrent - Note you need to be really careful with this kind of thing. Your  @"DirChanged" notification will fire on the second thread -- not on your main thread like you might expect. You must be careful to avoid any non-thread-safe behavior in your @"DirChanged" handler. See NotificationsAcrossThreads for more information. ]**

In my NSDocument subclass I add self as an observer of these DirChanged messages on the directory I'm currently viewing.  I do this each time I change which directory I'm viewing... Note that I remove myself as an observer of the previously viewed directory.

    
	if (current_dir != nil) {
		[[NSNotificationCenter defaultCenter] removeObserver: self
			name: @"DirChanged" object: current_dir];
	}
	[[NSNotificationCenter defaultCenter] addObserver: self
		selector: @selector(dirChanged:)
		name: @"DirChanged" object: newDir];

	current_dir = newDir;	


So now, whenever the directory object finishes adding up the sizes of all those bundle files, it posts notification, my document gets that notification and calls dirChanged: below and I tell my NSTableView to refresh.

    
- (void)dirChanged:(id)sender
{
	[dirTable reloadData];
}


**[ MikeTrent - All of the code in dirChanged: must be thread-safe.] **

**[ TomWaters - I was concerned about this and was prepared to use locks to work around this issue, but in this case, telling an NSTableView to reloadData, it turned out to work fine so far.  In Java, this would be a simple matter of using some synchronized methods to be sure that the worker thread blocked while the main thread was in any critical sections.  Do you believe that using DO is the best way to synch the threads?  It seems like it would be nice if the main event loop had a monitor that you could enter in worker threads so you would block until it was safe.] **

**[ MikeTrent - Looking at the documentation for "reloadData", it looks fairly safe. It sounds like it mainly marks the view for redisplay; the actual redisplay will happen on the main thread. However, I believe reloadData will end the table's editing context if the table is in the middle of editing a cell, and I'm not sure if that's safe. If it's not you could defer the reload until the user finishes editing, or you could try loading only a portion of the table with [dirTable setNeedsDisplayInRect:[dirTable rectOfRow:changedRow]].

NSLock is the easiest way to protect access to critical region. Threads "lock" a lock when they enter the critical region and "unlock" it when they're done. This does add some runtime overhead, but I believe its more efficient than using DO.

But often you don't have direct access to the critical region (it's buried in some other library code) or there are other reasons why the code must be loaded on a specific thread (common when falling into Carbon or OpenGL code). In those cases, the worker threads need to request this action take place on the main thread. That's where DO comes in. The worker thread sends a message to its own app and blocks waiting for a reply, the message is received in the event-loop on the main thread ( it is on the main thread, right? ;-) ), the request is processed, and the reply is returned to the worker thread, which resumes. It's very clean, but it can add measurable overhead.

So in a sense you can think of DO as the monitor you propose. But rather than having the main thread block until the worker thread completed its work, the worker thread blocks and the main thread provides its request.

I think the moral of the story is you need to be careful when using threads. Using them carelessly can cause unpredictable crashes or add messaging overhead. I do not believe threads replace the need for asynchronous program flow, though they do help. For example, in your application it may be better (in terms of memory usage and filesystem perfomance) to detach one thread to compute the size of many subdirectories (returning results periodically) than it is to detach many threads each looking at one subdirectory.

This is one hell of a tangent, and doesn't really have anything to do with Notification code except to point out notifications fire on the thread they're posted from. How can we arrange this in a better format? ]**

If you are destroying the object that is observing notifications, don't forget to remove the observer...

    
- (void)dealloc
{
	if (current_dir != nil) {
		[[NSNotificationCenter defaultCenter] removeObserver: self
			name: @"DirChanged" object: current_dir];
	}
	return [super dealloc];
}


I hope this helps!

TomWaters

