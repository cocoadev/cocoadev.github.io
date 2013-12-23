---
layout: page
title: NSNotificationCenterProblems
---

I'm having some problems with NSNotificationCenter's method addObserver:selector:name:object. When I use the following code, the selector gets called no matter what object is sending the notification even thought I only want to receive notifications from the object quickSearchField.

    
- (id)init
{
   if (self = [super init])
   {
      NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
      [nc addObserver:self
         selector:@selector(quickSearchChanged:)
         name:NSControlTextDidChangeNotification
         object:quickSearchField];
   }
}

----
I assume you are trying to do this in an object you wired up in IB. The better way to handle this is the following...

Override **- (void)awakeFromNib** and do it there, checking that quickSearchField is not nil before you do so...

    
- (void)awakeFromNib
{
	if (quickSearchField)
	{
		NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
		[nc addObserver:self
			selector:@selector(quickSearchChanged:)
			name:NSControlTextDidChangeNotification
			object:quickSearchField];
	}
}


What you are experiencing is that when you are requesting the notifications, your control outlet is still nil. I don't think the state of your outlets can be relied upon until **- (void)awakeFromNib** as other parts of the connection hierarchy may still be being constructed.

When you called **- (void)addObserver:selector:name:object:** your control outlet was still nil, which effectively told the notification center, I want this notification from all objects, which is what you described.

-- NeilVanNote

----
Thanks, now it works. Is it correct to remove this notification from the notification center even though I added the notification in the awakFromnib method and not the init method?
----
I am not sure if I understand your question correctly, but it shouldn't matter when you **- (void)removeObserver:name:object:** in relation to **- (void)addObserver:selector:name:object:** just as long as you add and then remove.

-- NeilVanNote

----

And now that this issue is resolved, it seems like a good time to plug the time-honored practice of using a debugger. The best thing you can do when learning any language, library, or source base of any kind is step through code in a source-level debugger. Mac OS X's ProjectBuilder and XCode provide a graphic front end to UNIX's GDB debugger -- it's a simple matter to set breakpoints, examine values, and so on.

In this specific case, the originator was able to identify the line of code in question, but wasn't able to figure out what was wrong. If *I* were the originator, I would have set a breakpoint on my "addObserver:selector:name:object:" method in "init" and ran my program; when the break point hit I would look at the values of the variables being passed into the "addObserver..." method and make sure everything met my expectations. "Huh," I think to myself, "it looks like quickSearchField is nil. I wonder if that's causing my problem". A quick trip to the NSNotificationCenter documentation would explain what a nil object means. "Huh, none of my outlets have been initialized yet", I think next. A quick trip to NSBundleAdditions or maybe even reading through www.cocoadev.com would quickly uncover the issue. Another Cocoa success story.

So, next time check out the code under the source debugger and make sure your code works the way you expect it to. If you're still stymied, go ahead and post the question here. But maybe you'll learn the answer on your own -- and that will save you precious minutes/hours in your development process.

-- MikeTrent

