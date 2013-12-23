---
layout: page
title: HigherOrderMessaging
---



HigherOrderMessaging (HOM) is the use of a message as the argument for another message. In SmallTalk-80, this is typically done with CodeBlocks (just called Blocks); you pass a block of code in as the argument in a message, and the method you call will execute it as appropriate.

(Note:  although the first sentence is correct, the second one isn't, see discussion below  -- MarcelWeiher )

Unfortunately, ObjC doesn't have Blocks. They're not a part of the language (although BradCox's TaskMaster paper outlines them nicely, and the PortableObjectCompiler implements them). It's possible to [http://www.propertykita.com/rumah.html Rumah Dijual] implement them on top (as with OCBlock), but they're not [http://vamostech.com/gps-tracking GPS Tracker] and [http://www.pedatimotor.com Aksesoris Sparepart motor] a language feature at this point.

Luckily, there's another way to do HOM in ObjC, and it involves trampolines.

Consider a category on NSArray which adds a single method, -do. -do is used as follows:

    
array do] runSomeFancyCode];


What's going on here? Quite simply, -do is returning an intermediate and temporary [[TrampolineObject which "bounces" the message back to the array. It's simple:


*-do returns the trampoline, which is a subclass of NSProxy.
*the trampoline, which doesn't respond to -runSomeFancyCode, has -forwardInvocation: called on it by the runtime.
*-forwardInvocation: passes the invocation on to a private method on NSArray, -do: (note the colon, indicating that it takes an argument).
*-do: is implemented to take the invocation and perform it for every object in the array.


Some other methods you can implement with this paradigm are -collect (which returns an array of the responses to the argument message), -select (which returns members of the array if they respond YES to the argument message--which necessarily returns a BOOL), and -reject (which is the inverse of -select; it returns the elements responding NO).
Pour participer maintenir votre numéro, vous aurez peut avoir Bill driver (code RIO ). Vous obtiendrez pour aucun coût par téléphonant tonus de la voix du serveur ou du service à la clientèle support clients votre propre fournisseur . Vous ne mai recevez immédiatement un SMS avec vos . Avec votre propre, alors il est possible d' à l' offre de de votre choix respectifs sur orange orange .

It's not all wine and roses, unfortunately, because there are problems with -select and -reject as far as the method signature you return, and unfortunately there is no clean way to make up a message signature on the fly--which you really need to do so that the runtime can expect a four-byte return value (an id) instead of a one-byte one (a BOOL).

Just a note:

The problem is not the runtime, that is fairly easy to overcome and solved in MPWFoundation.  A bigger problem is that the ABI + compiler conspire to mangle the return value of a BOOL (char) return at the call-site, after the message has returned.  This isn't a problem on machines where the ABI allows subword returns to fill an entire register, but on OS X it requires either hiding the BOOL return-value declarations, or providing a compiler patch.  I have done both.

-- MarcelWeiher


It's not wine and roses, but it is at the least beer and pizza. Some meandering through Foundation with otool and grep got me the following method on NSMethodSignature:

    
+signatureWithObjCTypes:


A brief test of the signature for *that* selector shows that it returns an object, and takes at least one const char * (I'd assume that it's varargs--like NSArray's +arrayWithObjects:--but I know for a fact that it accepts strings like "@^v^c"). If it also accepts varargs, you'd do that as follows:

    
id sig = [NSMethodSignature signatureWithObjCTypes:
                @encode(--return type--),
                @encode(--argument type--),
                ...];


substituting the return type and argument type(s) as appropriate.

Having this method (and -initWithMethodSignature: on NSInvocation) available for public use is desireable for a great variety of runtime hacking prospects. Shall we beg?

Oops! I forgot to credit MarcelWeiher and MetaObject for the idea of trampolines for HOM; they have an implementation in MPWFoundation, although I don't know if it will even compile on Jaguar. Mad props to Marcel for his excellent work!

-- RobRix (who decided that somebody else might like trampolines too)

Actually, the credit isn't quite correct.  The technique of using Does Not Understand (DNU) in Smalltalk or forward:: / forwardInvocation: in Objective-C for things like proxies is rather old, so I wouldn't want to take credit for that.  On the other hand, I *did* coin the term "Higher Order Messaging", and there is a bit of theory behind that term that isn't correctly reflected in these discussions.

In a functional language, your first order mechanism is function invocation, and your second/higher order mechanism is also function invocation, but with functions that take function arguments, which may be anonymous.

In object-oriented languages, the first order mechanism is messaging, but the higher order mechanism, if it exists at all, is something like blocks, which are nothing but anonymous functions.

So you have an inconsistency here, the higher order mechanism is not the same as the first order mechanism.  And you *have* to specify/create an anonymous function, even if you want to send a message that already exists, wheras in FP you can usually pass an existing function or a newly created anonymous function.

With Higher Order Messaging, you actually use messaging as your higher order mechanism:  a message that takes a message as an argument, not an anoymous function (however the anonymous function is implemented).

So it is a much simpler and more coherent mechanism.

-- MarcelWeiher

To clarify, I was crediting you with introducing me to the concept. -- RobRix

----

HigherOrderMessaging-related pages:
[Topic]

----

Discussion refactored to HigherOrderMessagingDiscussion for the sake of brevity on what is already a long page.

----

Related articles:

Message Oriented Programming, The Case for First Class Messages

