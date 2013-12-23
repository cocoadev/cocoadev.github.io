---
layout: page
title: FakeNSNumbers
---

I've been playing around with the "Extra Bytes" of an NSNumber instance and figured out how to fool an NSDictionary into thinking that it is dealing with a real key object. I don't know know how safe this is to do, but the overhead you save from not having to allocate memory, register with the autorelease pool and sending extra messages makes this a tempting idea. 

I've been using NSDictionaries to do most of my hashing, and whenever I'm able to key objects with int values I hate the idea of having to create a bunch of NSNumber instances just for the purpose of providing a key value. FYI, keying a dictionary with NSNumbers makes lookups ten times faster than keying a dictionary with NSStrings. Unless you permanently keep a range of NSNumbers around just to use as keys, you usually have to create a new NSNumber every time you set an object and every time you send an objectForKey message. Maybe there is a better way to hash in Cocoa, so if anyone knows of one, I'm all ears. 

Anyways, here's a structure I came up with that acts like an NSNumber. 

    
 typedef struct _UmFakeIntNumber {
     Class isa;
     short retain_count;
     short info;
     int value;
 } UmFakeIntNumber;
 
 UmFakeIntNumber UmMakeFakeIntNumber(id number) {
     int *integerPointer=(int *)number;
     UmFakeIntNumber fakeNumber;
     fakeNumber.isa=integerPointer[0];
     fakeNumber.retain_count=1;
     fakeNumber.info=integerPointer[1] & 0xFFFF;
     fakeNumber.value=[number intValue];
     return fakeNumber;
 }


Below is an example of how to use this structure as an NSNumber object:

    
 UmFakeIntNumber fakeNumberStructure=UmMakeFakeIntNumber([NSNumber numberWithInt:0]);
 fakeNumberStructure.value=3;
 id fakeNumberObject=&fakeNumberStructure;
 NSLog(@"fakeNumberObject: class: %@ value: %i",
           NSStringFromClass([fakeNumberObject class]), [fakeNumberObject intValue]);
 fakeNumberStructure.value=5;
 NSLog(@"fakeNumberObject: class: %@ value: %i",
           NSStringFromClass([fakeNumberObject class]), [fakeNumberObject intValue]);


*2003-07-29 18:56:43.869 test[5015] fakeNumberObject: class: NSCFNumber value: 3*


*2003-07-29 18:56:43.886 test[5015] fakeNumberObject: class: NSCFNumber value: 5*

You can use this structure to set the key values for objects you store in a dictionary, and once the object has been set, you are free to change the value member to some other value without having to worry about the dictionary changing the key value for the original object!!!

    
 id dictionary=[NSMutableDictionary dictionary];
 id object1=@"object1";
 id object2=@"object2";
 fakeNumberStructure.value=1;
 [dictionary setObject:object1 forKey:fakeNumberObject];
 fakeNumberStructure.value=2;
 [dictionary setObject:object2 forKey:fakeNumberObject];
 fakeNumberStructure.value=1;
 id returnObject1=[dictionary objectForKey:fakeNumberObject];
 fakeNumberStructure.value=2;
 id returnObject2=[dictionary objectForKey:fakeNumberObject];
 NSLog(@"returnObject1: %@ returnObject2: %@", returnObject1, returnObject2);


*2003-07-29 18:56:43.899 test[5015] returnObject1: object1 returnObject2: object2*

The only problem with doing this is that Apple has all the rights in the world to change the data structure around because this is breaking the rules of encapsulation. I wonder how long the "Extra Bytes" have been set this way for NSNumbers. Oh well, just food for thought. --zootbobbalu

----

This is definitely a PrematureOptimization. Always avoid optimizing things because you "hate the idea" of something happening. :-) A handful (ok, a Mickey Mouse handful) of reasons off the top of my head why you shouldn't do this:

First, it relies upon the private instance variables, which are implementation details of NSNumber that are subject to change. Don't ever release a shipping application that does this, because it will probably break with some future release of the OS.

Second, and more subtly, it only works because NSNumber's implementation of -copy returns a new instance. This is another reliance upon an implementation detail. An NSNumber is normally an immutable object (can't change it after you create it) so its implementation of -copy could easily be changed by Apple to just call -retain, and thus keep the same instance. NSDictionary always makes a copy of the keys inside it, so if NSNumber's -copy changed to -retain, the dictionary would be storing the stack-based instance as a key. Then when you twiddled the value you'd break the dictionary because you're modifying all the keys (they'd all be the same instance).

Third, it adds complexity into the code. For an outside programmer who's looking at the code, they're going to wonder what the heck you're doing, and they'll waste time trying to figure out what UmFakeIntNumber is and why you're using it instead of     +[NSNumber numberWithInt:].

Fourth, the idea is to avoid an allocation. It does achieve this, partly, but really it's a non-optimization. Does the program's performance suffer in any significant way from the speed of NSNumbers that are allocated to access a dictionary? Almost certainly not. So there's no actual benefit in practice.

To sum up: Using a trick like this will basically make your code fragile, hard to read, and it won't get any faster. Not really a good tradeoff!!

That's not to say that zoot was doing a bad thing; it can certainly be interesting and fun to explore the internals of AppKit and Foundation, and to try to figure out how they work. Some of the best OS programmers I know got started because of their inherent curiosity and willingness to sit with a debugger to find out how things were implemented. But please, use that knowledge for good -- don't ever think about doing anything remotely like this in real code! :-)

-- DrewThaler

In like fashion, I shall chime in: I am impressed, zoot, that's incredibly cool. But good lord. What an evil sort of thing (:

-- RobRix

----

Thanks for the comments Drew, but I disagree about a couple of your points. First, this is only a PrematureOptimization if you haven't checked if it is really needed. I happen to be in the process of improving a high level data source object, and I would really like to keep using an NSDictionary as a lookup table. In my case, this isn't premature because I already have a proof of concept, I have already profiled and the access times are hindered by the dictionary. Yesturday I did some additional tests and found that using this fake number makes lookup times two times faster. So, to anyone that uses a dictionary for a large number of objects (on the order of tens of thousands) I think NSDictionaries can be slow. 

BTW, I'm filling the dictionary with a modified string object that lets you access the character buffer directly, so messaging is very minimal in this data source object. This is why the performance of the dictionary started to become a factor. 

I agree that breaking encapsulation rules is very very very very bad, so I'm totally on the same page about your comments that this will create unstable fragile code. The title of this discussion kind of alludes to the fact that this isn't a good thing to do. I was hoping that someone in the know might spot this and say that the "Extra Bytes" of an NSNumber have been and will always be structured this way and that an NSDictioanry will always copy key objects, but that is wishful thinking :-)

I did a test in ObjectAlloc and found that an NSDictionary does in fact copy all key objects. After seeing this yesterday I started to feel more uncomfortable with playing with the "Extra Bytes". I was probably going to update this post with that finding, but you have already done that!! I guess the moral of the story is you shouldn't be using a high level object to do low level tasks. I've already started writing a custom hashing function in C. Maybe someone can recommend a better way to hash in Cocoa. I'm still all ears to any advice that will save me from reinventing the wheel. My design needs only require the use of a lookup table keyed with integer values that buckets Cocoa objects. 

As to the point that this will add complexity to my code. I don't think coding at a lower level should be off limits. You are right that the my code will become less readable, but sometimes you just have to get into bit flipping mode to do things faster. As long as you isolate users from the complexities of your implementation, I think coding at a lower level is OK. Plus, it would be silly to suggest that people writing graphics filters should manipulate pixels by sending messages to bit objects. I don't think you are saying that an NSDictionary is the only way to hash in Cocoa.

I would like to make the point that the main purpose of this discussion is to help me with my need for a faster hashing function. Thanks again for your comments Drew. I probably should have done a better job at warning people not to try this at home, but I didn't realize all of these pitfalls until I started to play around with this structure. My only defense is that I did state my ignorance in my second sentence! And yes Rob, this is an evil sort of thing!

--zootbobbalu

----

As much as I like Cocoa, if you're really optimizing down to that last few percentage points of speed, you can definitely get faster performance by avoiding ObjC messaging altogether in speed-critical portions of your code.

An objc_msgSend has a lot more overhead than a C function call ... typical numbers quoted for Jaguar are 20 CPU cycles vs 0 cycles, respectively. That's cost basically represents the cost of the dynamic message dispatch which makes ObjC so cool. The overhead really isn't terrible for normal usage, but when you're optimizing a tight loop you can gain a lot by avoiding it. 

Cocoa also has a tendency to use a lot of objects, and copy objects around. Again, this is not really bad in general, but it can be a killer when it happens somewhere that you're micro-optimizing. Reminds me of the naive C++ coder who wrote     pixel = new CRGBPixel(x,y); pixel->SetColor(r,g,b); delete pixel; in a loop, and wondered why his blitter was so slow. ;-)

If you want a faster hashing function which handles arbitrary keys and values (ie, without necessarily copying stuff around in the heap), you don't have to look far. Have a look at CoreFoundation's CFDictionary. With CF you can use raw integers for keys or values by creating the dictionary with a callbacks structure that has the appropriate callbacks (retain, release, equal) set to NULL. You can also specify your own hash routine, which may help optimize the distribution of buckets. That's the nice thing about CF -- since everything goes through callbacks, you can use CF's collections to store not just CFTypes and NSObjects, but also things like malloc'ed pointers, vm_allocated buffers, etc. Thanks to TollFreeBridging, using CF in a Cocoa app is really pretty painless ... just make sure you always use the CF APIs to access any collection that has custom callbacks.

-- DrewThaler

----

Please see NSMapTable and use it.

