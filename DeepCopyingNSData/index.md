---
layout: page
title: DeepCopyingNSData
---

In my app, I have an array of NSData objects containing data (array of floats) that must not be changed.  I need copies of this that can be changed, so I used the copy method.  However to my surprise I found that the "bytes" pointer is exactly the same in the copy-ed object, and so changes to the bytes of the copied object obviously change the original. I can't imagine ever wanting this (otherwise I'd use a reference to the original object) so I implemented a category method on NSData overriding the NSCopying protocol, to actually copy:

    
- (id) copyWithZone:(NSZone *)zone
{
  return [self class] allocWithZone:zone] initWithBytes:[self bytes] length:[self length;
}


which solved my problem and I was happy.  Until my windows stopped coming up, and I (eventually) tracked it down to here.  When trying to load some nibs, the above line of code kills it with 
**
*** initialization method -initWithBytes:length:copy:freeWhenDone:bytesAreVM: cannot be sent to an abstract object of class NSCFData: Create a concrete instance! 
**

I tried to see where things went wrong:
    
- (id) copyWithZone:(NSZone *)zone
{
	Class shouldBeNSDataClass = [self class];
	NSData* shouldBeAConcreteInstance = (NSData*)[shouldBeNSData allocWithZone:zone];
	return (id)[shouldBeAConcreteInstance initWithBytes:[self bytes] length:[self length]];
}

and shouldBeAConcreteInstance ""seems"" to indeed be a concrete instance, but I'm not sure how to be sure. 
So:  Is there a way to fix my copyWithZone to prevent this? Is there a better approach for my main goal (copy NSData with a distinct chunk of memory)?  
Thanks!

----
If you are going to be modifying the contents of an NSData then you must use NSMutableData. NSData is for immutable data *only*. You are not allowed to touch its contents. Since you can't change them, it can save a copy by not actually making a copy of the data but only by referring back to the old pointer. You'll find that if you use NSMutableData, the copy will be performed as expected.

----

Your confusion stems from not having understood the Cocoa     copy semantics.  Please read http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_8.html and the last section of http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Tasks/ImplementCopy.html .  Key point: *Where the concept "immutable vs. mutable" applies to an object, NSCopying produces immutable copies whether the original is immutable or not.* --K

---- 

I also seem to have been confused about editing on this site.  Just as I finished writing it I ran into some documentation suggesting, as has since been pointed out, that I should look into NSMutableData more carefully. I removed the CocoaDiscussions header, and figured it wouldn't be seen until I'd had a chance to reconsider the question.  Guess not :)   Thanks for the help, I actually had one more question about why casting to mutable didn't work that Chap 3 Sec 8 cleared up.

I changed how I was using NSData objects and added a category method to get mutable copies
    
- (NSMutableData*)mutableCopy
{
	return [[NSMutableData alloc] initWithBytes:[self bytes] length:[self length]];
}


----
As for why removing CocoaDiscussions didn't work, see RecentChanges.

Your category is extremely odd, because there's already a     -mutableCopy method which does exactly what you want, so there's absolutely no reason to add your own. I would suggest that you spend a little more time reading the documentation before you jump in and start implementing categories on Cocoa classes.

