---
layout: page
title: SmallTalk
---



Correct name is Smalltalk (the "t" is not a capital letter).

Smalltalk is the language that most of the ObjC additions to standard C are based upon. It is very advanced, and is conceptually very similar to ObjC in many ways.

FScript provides Smalltalk-like scripting for Cocoa objects.  

Information about Smalltalk can be found at Smalltalk.org [http://www.smalltalk.org]

Cincom [http://smalltalk.cincom.com/index.ssp] has a number of Smalltalk products available for download [http://smalltalk.cincom.com/downloads/index.ssp].
They have built a news aggregator, Bottom Feeder [http://www.cincomsmalltalk.com/BottomFeeder/], that runs under OS X.

AmbraiSmalltalk [http://ambrai.com] is a newcomer, and it looks very promising.

Squeak [http://www.squeak.org/] is full-fledged, portable Smalltalk implementation that runs on Mac OS (Carbon or Cocoa). It's derived from Apple's Smalltalk-80 environment for the Mac.

Free Smalltalk books [http://www.iam.unibe.ch/~ducasse/FreeBooks.html]

----

**Discussion**

Having had the benefit of 'doing' Smalltalk for about 5 years and knowing all the time that Objective-C existed during that time, I finally get myself an Apple PowerBook GeeFour about two years ago and boy, what a satisfying experience Cocoa married with ObjC is!!! OK, not perfect, but then, it could be Windows...anyway, to the point.

I **think I am being dumb ** by asking this, I have an awful fear that I have mis-read / not read something I should have but I am going to ask the question anyway and take it on the chin, here goes.

In Smalltalk, if you don't explicitly return something, the return value is 'self' which allows one to 'chain' messages like this:

    
  (anObject msg1 msg2: arg) msg3.
  "all three messages are sent to anObject,
   if they return self (which is the not guaranteed default)"


...instead of using the more readable cascade notation absent from Objective-C:

    
  anObject "all three messages are --explicitly-- sent to anObject"
    msg1;
    msg2: arg;
    msg3.


In Objective-C I seem to have to say this:

    
  [anObject msg1];
  [anObject msg2: arg];
  [anObject msg3];


when what I would like to be able to do is:

    
   [anObject msg1] msg2: arg ] msg3];


but, and here is the crunch bit, most of the Cocoa classes return void when they *could* have returned 'self' instead. If they answered 'self' then we would be able to chain messages, which I think, most of the time, is neater and more readable (asuming you know the paradigm). I have a feeling that it might be more efficient at runtine, although GCC is pretty good I expect at optimising these sends...isn't it?

*It could only be less efficient, actually. Where exactly do you think the send would go?!*

I also have [[GNUStep on my Linux box and that does the same....anybody think it could be worth considering?

Anybody care to comment, put me right, whatever....?

Sean Charles

----

I'm not sure how readable that would be. To give an example:

    view setNeedsUpdate:YES] doSomeOtherViewStuff];

loses some coherency, to my mind, as it dilutes the use of e.g.:

    [[object delegate] doSomeDelegateStuff];

I would much rather write:

    [view setNeedsUpdate:YES]; [view doSomeOtherViewStuff];

and that is, in fact, what I do write. IMHO stuff, I suppose.

 -- [[RobRix

----

*It could only be less efficient, actually. Where exactly do you think the send would go?!*

Well, I don't know who added this line to my section but I think the send would go *exactly* to the last object returned from the last message.

*Same place as the other code. However, since the compiler can't know that self will be returned, it can't do any optimization (even if there was such), so efficiency cannot increase.*

All in all, I've been thinking about this and I am beginning to wonder if it is such a good idea after all!!! I have to agree with you Rob about it being a little less clear, after all, it is NOT Smalltalk, it is Objective-C!

I think I'll just shut the --- up and get on with stuff.

-- Sean Charles

----

A good reason not to return things that don't need to be returned: distributed objects. You really don't want to be passing unnecessary data back and forth.

I find these deeply chained calls a real pain in the neck to debug.  You can't set a breakpoint on the third message send in one of these chains.

*Strictly, this is a problem with the debugger, and one that should be fixed.*

----
Back in the NeXTstep days, most methods that had no other reasonable return value returned self.  The chaining described above was possible and common.  **Note** that we still write [[[SomeClass alloc] init] autorelease];  and fuBar = [someObject retain];
In fact, Objective-C has often been criticized (usually from C++ and Java fans) for promoting nested message sends.  *Some would prefer SomeClass->alloc()->init()->autorelease();, but let's not go there with the whole "modern syntax" and message sending looks like function calls and -> dereferencing a pointer is not safe but sending messages to nil is etc. etc. etc.*

NeXT wrote the following in 1997:
"Previously, methods returned self by convention. Some methods return self to 
indicate success and nil to indicate failure. Returning self to indicate a 
Boolean value or returning self without any associated meaning made the API 
more confusing."

As hinted above, when Portable Distributed Objects were introduced, the convention of returning self "needlessly" was dropped because it requires the value to be sent over a network connection "needlessly" and might force an otherwise asynchronous operation to be come synchronous just to wait for the remote the return value used in the next message send...

Here's a discussion from 1997:
http://groups.google.com/group/comp.sys.next.programmer/browse_frm/thread/9d5320321a2bc9e9/89a8fa43beebdd91?lnk=st&q=group%3Acomp.sys.next.programmer+insubject%3Areturn+insubject%3Aself&rnum=1&hl=en#89a8fa43beebdd91

Here's a discussion from 2003:
http://groups.google.com/group/comp.lang.objective-c/browse_frm/thread/84fb5e1fb18b8bed/a9ff1b168c1748c9?lnk=st&q=group%3Acomp.lang.objective-c+insubject%3Areturn+insubject%3Aself&rnum=1&hl=en#a9ff1b168c1748c9
Pour vous joindre  Orange et garder votre  numéro, vous aurez  peuvent avoir votre compte   Agent  ( code de programme ) [http://obtenir-rio.info numéro rio]. Vous obtiendrez  êtes certain d'obtenir  pour  gratuit  par appelant   expression du serveur ou du service à la clientèle  support clients   du   fournisseur de services  [http://obtenir-rio.info/rio-bouygues code rio bouygues] . Vous ne  mai   get un SMS avec votre . Avec  du  [http://obtenir-rio.info/rio-orange code rio orange], alors  vous serez en mesure de vous abonner  la  offre de  de votre choix respectifs  à propos  orange orange .

