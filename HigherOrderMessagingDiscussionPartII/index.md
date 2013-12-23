---
layout: page
title: HigherOrderMessagingDiscussionPartII
---

To continue the discussion at HigherOrderMessagingDiscussion (which got to the max size):

    forward:: indeed eliminates all the problems with NSInvocation and NSMethodSignature, and it's also much faster, but it still has the problem of generating a warning when doing     NSArray *a = collection select] hasPrefix:@"a"];
I experimented with this approach for a while until I figured it's all just wrong. Higher Order Messaging is all about passing a message as an argument, meaning a message should be transformed into something concrete. So after banging my head against the [[ObjC runtime for a while, I came up with a working solution that actually lets you create a message object out of any known message, and then you can pass it as an argument to any other message. Using this approach looks like this     NSArray *a = [someArray collect:MSG(dataUsingEncoding:NSUTF8StringEncoding)];

The MSG() macro returns an instance of a Message class, which holds the selector of the message and its arguments list. This can then be dispatched to any object using the objc_msgSendv() functions.
In practice, the MSG() macro sends the passed message to a special message builder object. That object then scans all registered classes for a method with a selector of the passed message, that takes the largest number of arguments. It then creates a new arguments list, copies the arguments of the message to it, and returns a new Message instance with these arguments. Of course it caches the method so that you won't have to scan all classes every time you use the MSG() macro, but when you already know the size of your arguments list, you can save the time of looking up the method for each object you're going to send the message to. According to my tests, using this approach is up to 80% faster then my previous implementation that uses NSInvocation and NSMethodSignature, and if you cache the Message instance and have lots of loops to do with it, it's sometimes even faster then using NSEnumerator (although     objectAtIndex: is still much faster).

The code is in its early stages, but worked in all tests I did. The implementation is currently available only for NSArray, and the code is a bit messy in some places, but like I said it's still in progress. You can get it at http://svn.dpompa.com/hom/trunk.

--OfriWolfus

----

Very nifty, but I'm a bit wary of using macros in general due to the poor (read: lack of) integration with the language as a whole. Case in point: any message you might be sending that would involve commas. For example     [NSArray arrayWithObjects: @"foo", @"bar", nil] is an instant no-no. These are probably less common than simpler ones, but common enough.

Also, the warnings don't bother me at all. If they did, I'd use quiet little out-of-the-way informal protocols, although I realize not everyone likes doing so.

That said-- quite novel, and I'm intrigued indeed. -- RobRix

----

That's not the real problem as my MSG() macro is simply defined like this:
    #define MSG(X...) ID(([_sharedMessageBuilder X]))
where ID() is another macro that tricks the compiler to think the message returns id in all cases. Since it's defined to take a variable number of arguments, any message with a return value works fine.
The only problem is when you need to get a message object from a message with void return value, but for that I added a     voidMSG() macro that's defined like this:
    #define voidMSG(sel, ...) objc_msgSend(_sharedMessageBuilder, @selector(sel), __VA_ARGS__)

--OfriWolfus

----

Just a pointer to (yet another) HOM framework called  Cocoade Frameworks:  http://www.cocoade.com/developer.html
There seems to be pretty good documentation available online. Anyone already familiar with it here?

----

I happened to wander by here after discussing HOM with somebody and took another look at this implementation. That     ID macro is really scary! I'd like to suggest that rather than this risky assembly stuff, it would be better to use a thread-local variable. Throw out the     ID macro and have your trampoline store the invocation in the thread-local variable instead of returning it. Then the     MSG macro can call the trampoline, then extract the result from the thread-local variable. It'll avoid the scary assembly and should be platform-neutral. -- MikeAsh

