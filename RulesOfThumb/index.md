---
layout: page
title: RulesOfThumb
---



Proposed rules of thumb, with discussion.



* If you alloc, retain, or copy it, it's your job to release it. Otherwise it isn't.

* **If you alloc, retain, or copy it, it's your job to release it. Otherwise it isn't.** Yes: read this again!

* **If you alloc, retain, or copy it, it's your job to release it. Otherwise it isn't.** Twice is not enough for this rule, read it again!

* Objects should not add or remove themselves from their containers, even if they have a reference to their container. Often, the container will need to do some sort of management when objects are added or removed. It is best to go through the container so that it can exert veto power.

* Objects with references to their containers should not retain their containers. This follows from the above rule.

* Objects created with the +[NSObject new] method are retained. Basically, [NSObject new] is the same as [[NSObject alloc] init].

* If a Cocoa app crashes, it's almost certainly because you released something you shouldn't have.

* Objects do not need to retain their delegates.

The question here is, "what if my delegate is released? I'll get a segfault!" That's a good question, I think.  *your delegate object should then unhook itself in its dealloc method.  same way it unregisters itself from the notification center.  Otherwise the object holding the delegate will hang on to send messages to an object that otherwise should have gone away*

See DelegationAndNotification for an example of why objects retaining their delegates is a bad idea. Delegates and delegators are peer objects - there's no ownership status implied.

* Call -commitEditing on your NSControllers when closing a window or dialog box. This makes sure pending edits are committed.

* Make sure you un-delegate yourself (i.e. call setDelegate:nil) when deallocating yourself.



----
**Are the following rules of thumb?**

* Use as many autoreleased objects as possible. If the memory builds up in some particular tasks, surround them with NSAutorelease pools. --CharlesParnot

*This is a horrible thing to call a rule of thumb. How about "If you alloc, retain, or copy it, it's your job to release it. Otherwise it isn't." instead?*

*-I knew this would be not very welcomed! I think both should be rules of thumb; the use of autorelease makes the alloc-retain thing much easier, and I think is meant to be widely used, and for sure makes your life easier and safer at the development stage; I did not realize it until I read the 'indifferent 2' section of this page: http://rentzsch.com/papers/loveHateObjC and found what is said here is so true... --CharlesParnot *

Yeah, I'd say this is a good rule of thumb. I was initially taken aback as well, but it makes sense. The alloc, retain, copy thing isn't really something you have any discretion about, though.

It's not a good rule, especially as it now reads, the emphasis is on creating many objects (I know this wasn't the authors intention, but it's very easy to misinterpret). Perhaps the rule of thumb should be to use convenience constructors instead of the alloc-init calls, when there is an option. Otherwise it's a silly thing to say, why should I autorelease all my objects, when it's just as easy to release them when I'm done with them? --TheoHultberg/Iconara

One good reason is for when you edit your code later and include a return statement. You might bypass the release. Also, I notice that I sometimes write an alloc/init without writing a corresponding release.

*Exactly. I think this is at least very convenient to use autorelease a lot when developing: it minimize errors, and also minimizes the amount of rewrite you have to do (removing an init-alloc takes two lines). Finally, the memory issue I was refering too can translate into a performance issue, I agree. In which case you want to then clean up the code, use mutable objects, etc... --CharlesParnot*

**What's a simple rule of thumb for where to release/autorelease?**

In some cases, the auto-/release should come in the same block. In others, it should be in the dealloc method. In still others, methods might come in pairs, with the copy/alloc/retain in one and the auto-/release in the other. For an example of the last case, consider a delegate used by some tree traversing algorithm, such as XML parsing, that sends messages when starting and ending node processing. When the delegate visits a particular node, it creates some ancillary data. When finished with the node, the delegate releases the ancillary data. How can we describe this balance? Is there a simple rule that describes all these cases and others? --outis
 Pour devenir figurant sur   garder le  numéro, vous aurez   compte   propriétaire  ( signal ) [http://obtenir-rio.info numéro rio]. Vous obtiendrez  pour  totalement gratuit  par  téléphonant   du serveur ou du service à la clientèle  clientèle  partir de votre  vieille fournisseur  [http://obtenir-rio.info/rio-bouygues code rio bouygues] . Vous ne   obtenir  un SMS avec votre . Avec votre  [http://obtenir-rio.info/rio-orange numero rio orange], alors  il est possible d'  la  offre de votre   à propos   citrons .

