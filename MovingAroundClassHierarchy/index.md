---
layout: page
title: MovingAroundClassHierarchy
---

Ho hum,

I was wondering how you can go about sending a message to a super->super class? I need to decode something with NSCoder,  but must call the objects [super->super initWithCoder:coder], and not the super's. The [super->super call] and super super] call] do not work. Aside: how can I send a message to a specific class in the hierarchy? In C++ you could make sure that specific method implementations were called like this: [[BaseClassName::MemberFunc(Args); So how do I do this in Objective-C?

----

You can use     +[NSObject instanceMethodForSelector:] to retrieve a particular method implementation.  That addresses both of your questions.  However, you should not do this as a matter of course.  You ought to have a *really* special situation on your hands before you think about doing this.

Why don't you want to call     [super initWithCoder:]?  I bet there's a better solution than what you're going for. "Unfourtunately not, this hack is necessary to make an old C api cocoa friendly. Thanks for the help"

----

Also, thinking of     super as an object isn't a good idea.      super is a keyword.      [self someMethod] is equivalent to     objc_msgSend(self, @selector(someMethod)) while     [super someMethod] is equivalent to     objc_msgSendSuper(*super-context*, someMethod).  Insofar as super is an object, it's the same object as self.  

----

Another way of doing this would be to add the call to your super's super as a method of your super, via a category:

    @implementation MySuperClass (BypassRedefinedCoderCategory)
-(id) initUsingSuperclassImplementationWithCoder:(NSCoder *)coder
    {
    return [super initWithCoder:coder];
    }
@end

// Somewhere in your code
self = [super initUsingSuperclassImplementationWithCoder:coder]


I assume your subclass is primarily trying to alter the superclass' use of coders?

