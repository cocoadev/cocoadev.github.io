---
layout: page
title: QuickAutoreleaseQuestion
---



I have a really, really quick autorelease question that I can't seem to figure out. Does autorelease only send the release message to its object once its retain count is 1, or is there some other method it uses to determine when it should send the release message? http://goo.gl/OeSCu

-- MarcWeil
----
When an object is autoreleased, its reference gets put in the autorelease pool which is contained inside an NSAutoreleasePool class instance (duh). I think the pool clears this stack at the end of every run loop execution, but someone will have to confirm that for me. When it clears the pool, it sends a release message to all objects in the pool, no matter what their original retain count is. -- KevinPerry

----

To be specific: your object will receive a -release message for every -autorelease message received. That -release message is delayed until the current AutoreleasePool for the current thread is deallocated.

AFAIK, the current RunLoop will create an autorelease pool at the beginning of every iteration and release it at the end, and since the event loop is based on NSRunLoop, this behavior is inherited by the AppKit. And all is well with the world.

-- RobRix
----
So then that probably means that I should never use autorelease for member objects, or other objects that need to stick around for a long time, right? I guess my confusion comes from not knowing when I am going to have access to that object and when I am going to lose it (since my program uses AppKit and I don't control the RunLoop).

-- MarcWeil

----

Member objects should be retained when they are first made a member, and released (*not autoreleased*) either when they are replaced by another object (which should be retained in turn), or when *your* object gets deallocated.

This will ensure the lifetimes of your object and its members are coupled correctly.

(If your object has member setters that may be accessed synchronously by multiple threads, you'll need to be more careful. Better still, don't get in this position in the first place.)

Looking around on MemoryManagement, I can't see this fairly basic advice. Have we really forgotten to say it? Hmmmm, mayhap we need another refactoring over there...

-- KritTer
----
Yeah... I can't find this information at all in MemoryManagement. The problem with it (and most other references out there on this topic) is that all of them say this: "autoreleasing an object releases it sometime in the future". And then it is done with it, as if knowing this teeny little bit of information is going to be enough. *WHEN* will it be released is the important question, and an answer is needed that is more concise than simply "sometime in the future".

The biggest confusion would be knowing how to use memory management in different kinds of functions: setters, getters, etc. In a getter, does the object returned need to be autoreleased, retained, both, or neither? Does it make a difference on if the getter is for a member object or for something else? It probably does. When should it be used in normal methods? The only thing I have heard about that is that autorelease should be used in normal methods when you need to release something *after* a value is returned, in which case you know for sure that the autoreleased objects will be sent a release message as soon as the method exits. It would be nice and easy for this apply to everything (which might, in turn, make autorelease less powerful).

Right now, the only time I use autorelease is in methods that return a value that require allocated objects scoped in that method because in this case, I only need the objects around until the function returns, and then they are useless to me. Other than that, I have no idea when I should use autorelease because I am always afraid that the object is all of a sudden going to disappear on me when it is needed later in the program. This gets especially complex when an object is passed between many functions. When happens when an object is passed between 8 or 9 methods in a row? Some might retain, release, autorelease, etc. And then you have to factor in the built-in Cocoa objects such as NSArray and NSDictionary retaining all of the objects that are put into them. How the heck am I supposed to know whether or not my object is retained by something else when it comes time for an array or a dictionary to release it?

I hope you see how all of this can get very confusing, very quickly. Are there some simple rules of thumb for when to use release and autorealease? I have seen the Memory Management 101 article on CocoaDevCentral, but that didn't help any. It was too broad. I'm sure there are plenty of other people out there who share my confusions regarding this. Surprisingly, this is the only part of Cocoa I am really struggling with. Everything else about it I pretty much completely understand.

I guess this is turning out to be much more than a quick, quick autorelease question, huh? ;-)

-- MarcWeil
----
Pretty much, the rules are: 
* If you've *created* something that won't live longer than the function/method you're in (or the one that called it, if you're returning the thing), and you either can't be bothered to release it yourself or you don't want the calling code to have to, autorelease it.
* If you're *accessing* something and you're *not going to alter* what you got it from (e.g. an array), don't do anything. This applies to accessor methods as well as code calling accessor methods.
* If you're *accessing* something and what you got it from *might be altered before you're done*, retain it and then either autorelease straight away or release it when you're finished with it.
* If you have an object that will be alive *longer than the function/method you're in*, such as when you assign to a member of an object, retain it. Then release it later when you're done. Make sure there's no way for a user of your object to miss out on either the retain or the release, or you'll have a memory leak or a crash.


Some people argue that accessors should retain/autorelease the objects they return, because then you won't mess up later. The other alternative is to autorelease instead of releasing in the last rule. This depends on how rigidly you follow the above rules; if you never stray, you'll not need the cost it implies. Certainly, Apple's code never does, so getting to rely on it is bad from that perspective anyway. A personal choice, however.

Another argument sometimes put forwards is that "autoreleasing in an accessor provides thread-safety". It doesn't, it merely reduces the chance of something going horribly wrong.

-- KritTer
----
Okay, I think I pretty much understand it now. We'll just have to see if i can actually apply these things when I am coding :-). If not, I guess I'll just come back here and ask more specific questions.

Thanks for your help!

-- MarcWeil

*You are more than welcome to return and ask more questions, but please think twice before naming a discussion. The name of hte discussion becomes the title of the page, which should reflect the contents of that page.*

----

Sorry, just wanted to add one more comment.

Someone said:"*Yeah... I can't find this information at all in MemoryManagement. The problem with it is that all of them say this: "autoreleasing an object releases it sometime in the future".*"

I actually think that "autoreleasing an object releases it sometime in the future" is the best way to explain autorelease !! It will be released in the future... What is the future? Well, the future is not now, i.e. not in this method (to be more precise: the next time you have [pool release] where pool is an NSAutoreleasePool). So beyond that, you don't know and should not assume anything.

Also, I am not sure there was a clear answer to the initial question (!!) : autorelease DOES take into account the retain counts. It is really a delayed release. CharlesParnot

----

Conceptually you can think of autorelease like this:
    
NSMutableArray* ReleasePool = [[NSMutableArray alloc] init];

@implementation NSObject
...
- (id)autorelease
{
   [ReleasePool addObject:self]; // will retain!
   [self release];
   return self;
}
...
@end

Eventloop:
   while(GetEvent() != QUIT)
   {
      DistributeEvents(); // i.e. send action messages to application controllers etc.
      [ReleasePool removeAllObjects]; // release all autoreleased objects
   }


----

After reading this page, I still have a few questions... 

*
(Quote)
Someone said:"*Yeah... I can't find this information at all in MemoryManagement. The problem with it is that all of them say this: "autoreleasing an object releases it sometime in the future".*"

I actually think that "autoreleasing an object releases it sometime in the future" is the best way to explain autorelease !! It will be released in the future... What is the future? Well, the future is not now, i.e. not in this method (to be more precise: the next time you have [pool release] where pool is an NSAutoreleasePool). So beyond that, you don't know and should not assume anything.
*

Hm, ok. I have to say this answer would make autorelease pretty useless to me. I don't know, but I don't feel safe using something that will be release *some time* in the (near) future. Maybe my code works on my machine, but will it also work on a slower machine? Or a faster one?

Someone said, the Autorelease pool is released once everytime the runloop starts anew (or every time it ends). How do I know when the runloop ends? Is there some way to predict this? 
I know this has been retired, but maybe someone reads it, and helps me out here. Thanks!
-- Markus Walther

---- 

The runloop is the structure that waits for input (such as mouse clicks and timer fires), and triggers whatever code is supposed to be triggered.  When the triggered code finishes executing and returns, that's when the run loop cycle is over.  Basically, a cycle isn't over until you've finished everything that you're going to do in response to something and the app is back to waiting mode.

*Think of it this way: the autorelease system puts the onus of releasing an object onto whoever called you. Additionally, any methods you call that autorelease objects are also transferring responsibility to your caller. You can optionally take the responsibility yourself by creating a new autorelease pool; this may lower your memory footprint. Only if you've **not** been called by anyone - if you're writing the main function of a new thread - are you **forced** to worry about autorelease pools.*

----

Cool, that's what I needed to know. Thank you!

Markus Walther

----
Is it not good to have a single global auto release pool in all code, so that we our self will have the control over the releasing? 
sory as this is retired page!

----

*Hm, ok. I have to say this answer would make autorelease pretty useless to me. I don't know, but I don't feel safe using something that will be release *some time* in the (near) future. Maybe my code works on my machine, but will it also work on a slower machine? Or a faster one?*

Don't get confused between the concepts of "released" and "deallocated".  As long as you have sent a [yourObject retain] message to yourObject immediately after receiving an autoreleased reference to it, you should be fine -- you have incremented the retain count before the RunLoop has had a chance to decrement it.  In other words, when you send the [yourObject retain] message, the retain count of yourObject is greater than one, so even if the AutoReleasePool releases yourObject immediately after your method block (reducing its retain count by one), yourObject still has a retain count of at least one, so it won't be deallocated.  You do *not* need to know exactly when the "release" message will be sent to yourObject by the AutoReleasePool.  (In a non-multithreaded context.)  -- ErikPrice

----
Just to clarify on that multithreading comment, you don't have to worry that your objects will explode just because your app contains multiple threads. When passing objects between threads, the right way to handle them is to retain them in the "sender" thread, then autorelease in the "receiver". This way you're completely isolated from when the "sender" pops his autorelease pool and you never have any weird timing issues. Apple does this with all cross-thread communications (think     performSelectorOnMainThread:, DistributedObjects) and your code should too, if you ever write such a thing.

