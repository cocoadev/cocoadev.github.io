---
layout: page
title: HigherOrderMessagingDiscussion
---



Refactored from HigherOrderMessaging; see that page for context.

----

For a conceptually simpler implementation of HigherOrderMessaging(more flexible, too, I think) using CodeBlocks, see OCBlock.  In this implementation, as in Smalltalk, collections respond to -do:, which takes a block as an argument, as well as to -collect:, -inject:into:, -select:, -reject:, -detect:, -detect:ifNone:, and others...   Additionally, using blocks means that more than one instruction can be sent to each object in the array, and that the existing methods need not exist yet, and you can use each item as an argument...   --JoeOsborn

I'd disagree about greater conceptual simplicity with blocks; they are indeed more flexible, but there are tradeoffs on either side. -- RobRix

I also disagree about greater conceptual simplicity with blocks, strongly.  Blocks are, in a sense, anonymous functions that interact in magical an mysterious ways with their lexical scope.  HOM on the other hand is simply taking a message and distributing it, something that can be easily explained to complete novices.   -- MarcelWeiher

----

Getting back to the orignal issue:  It seems to me that to do what you want (implementing -[NSArray do]), you'd be better off just implementing something like these in an NSArray category:

    
// performs aSelector on each object in the array.  returns an array
// containing all of the return values.
- (NSArray *)arrayByMakingObjectsPerformSelector:(SEL)aSelector;

// performs aSelector on each object in the array, passing anObj as
// a parameter.  returns an array containing all of the return values.
- (NSArray *)arrayByMakingObjectsPerformSelector:(SEL)aSelector
                                    withObject:anObj;


I've used variations of things like this for years (I've never liked the Smalltalky names "collect:" and "select:").  This approach works without any sort of trampoline object, and without any need to dig into method signatures.  In your implementation, you just step through the array and use performSelector: to pass the message along.

Granted, 

    
newArray = [myArray
    arrayByMakingObjectsPerformSelector:@selector(foo)];


isn't as pretty as

    
newArray = myArray do] foo];


but the implementation is a lot more straightforward IMHO.  And if you want, you could always use "do:" as your method name:

    
newArray = [myArray do:@selector(foo)];


- [[JackNutting

----

The problem is, [array do:@selector(foo)]; is a pain in the butt if you're doing this sort of thing commonly. Trampolines collect the arguments as well as the selectors.

The layer of abstraction trampolines (or blocks, as Joe is sure to point out :) ) provide is almost vital here; I don't want to care if the collection is an array or a set, I don't want to have to add in whole bunches of -do:with:with: methods for every combination of arguments (especially since some or all of those arguments may not be objects).

So, -do:with:with:with: is not an option.

After talking with MarcelWeiher, I'm getting near to moving to Joe's blocks, but not just yet, not just yet. Trampolines are conceptually simpler (IMHO), and although it's taken some ad-hockery to work around a problem with gcc, I think I might have -select about to work (at long last).

-- RobRix

Yes.  In fact, Higher Order Messaging in its present form evolved out of an implementation of "EnumFilters" that presented a pipeline of message-based filters.  The filters were created using the "standard" forms, but the number of forms just kept increasing, and there were a number of different filters, so it was getting to be a real pain.  It took me a while to realize that all the "withSelector:with:" style messages really contained a second message-send that was dying to come out.  It then took me a little longer to figure out that the easiest way to represent a message-send in code was...wait...a message-send!  But the Aha! was great when it hit, and I also realized that not only does it solve my "exploding number of useless cover-methods" problem, but also was completely general and even looked a lot nicer.  -- MarcelWeiher

OK, I'll bite-- what's conceptually simpler about trampolines?  A block is just a bunch of code objectified.  Using blocks as iterators, I agree, might be conceptually more complex... but blocks are a much more powerful abstraction, and aren't just used for iteration.  The other problem with trampolines is that only a single line of code can be executed, and the receiver of the message specified has to be the result of the trampoline, right?  How to do string concatenation?

    
[mutableString appendString:[stringArray do]];

this obviously won't work, will it?  but with blocks:
    
[stringArray do:@{ :each | [mutableString appendString:each]; }];


I often don't have the element of the array as the receiver of a message... so, I really do need that extra capability.

--JoeOsborn


Just a note:  the above example that "obviously" won't work in fact *does* work.  Just say:
    
mutableString do] appendString:[stringArray each;



-- MarcelWeiher


In SmallTalk, I'd go with blocks. In ObjC, I think bouncing is conceptually simpler because forwarding et al is intrinsic to the language.

There's no question that blocks are the more powerful construct. I just don't need the extra power.

-- RobRix

----

I was bored, so I decided to implement of RobRix's above specification for a TrampolineObject. You can find the result at BSTrampoline, and feel free to use it for anything you like.

--ThomasCastiglione

Woohoo, good job, Thomas! Many congratulations and all. So the tests show it using -select properly and all? *Yep.*

I've been thinking of posting LSTrampoline as an example but since it's not working for -select/-reject yet, I'll wait on it. I'm gonna be taking a closer look at -select in your implementation, though, cos I'd like to be able to do [array select] rather than array collect] selectOn:...] which is what I'm currently looking at.

So thanks, Thomas :)

-- [[RobRix

Question:  does -select also work on OS X?  How did you work around the problem with the compiler / ABI?

--  MarcelWeiher


*No problem.*

Well, finally, LSTrampoline is done, working, and UnitTested (Joe will love that). And it doesn't crash, either! :)

-- RobRix

UnitTested!?!  RobRix UnitTesting?  VeryStrange!

Are they unit tests or functional tests?  Oh, well, at least you're testing externally to the object. (:  Carry on!

--JoeOsborn

Both unit and functional tests--i.e. LSHOMTest does what's expected of it (although I've not coded it using assertions or anything), and LodeStone is also behaving itself.

I think that the terms of our deal were that you had to code with efficiency of design in mind now ;)

-- RobRix

Hey, that's cool! I like the idea of select and reject, many times I have considered implementing something like:

    

- (NSArray *)objectsReturningValue:(long)value
                      fromSelector:@selector(someMethod)

and 

- (NSArray *)objectsReturningObject:(id)object
                       fromSelector:@selector(someMethod)


(and counterparts with 'withObject:' on the end) in a category on NSArray. Of course, the first one is redundant as it could be done using NSValues or NSNumbers, but it's more convenient to just pass in the value. They would of course make the objects in the array perform the selector, and if they returned the right value then they'd be put into the returned array. The second one would compare return values from someMethod with object using isEqual, but there should also be an objectsReturningObjectIdenticalTo version.

I keep wondering whether it should be toSelector or fromSelector or whenPerformingSelector or what... I guess it's not really the object returning the value anyway, it's the method itself. I'm finding it difficult to think right now because I haven't been able to use my Mac for several days.

I bet RobRix didn't expect me to post anything here.

-- AngelaBrett

RobRix was indeed surprised, Angela! Anyhow, to give some examples of how I'm using LSTrampoline:

    
// -drawRect: on LSCanvasView
-(void)drawRect:(NSRect)rect
{
...
[delegate elements] do] draw];
...
}

// -selectedElements on [[LSCanvas - this one amuses me :)
-(NSSet *)selectedElements
{
	return elements select] isSelected];
}


The transparency is something sublime.

-- [[RobRix

Sublime indeed, but it would be more amusing to use:

    
return [elements selectUsingSelector:@selector(isSelected)]


Hmm... it seems like it'd be quite easy to do a selectEnumerator type thing too.

Actually, I'm starting to feel a bit uneasy about this. Sure, monkeys do] eatObject:banana] has nicer syntax than 
    
[monkeys makeObjectsPerformSelector:@selector(eatObject)
                         withObject:banana];

especially when thinking in terms of English. But as soon as you separate out the two messages (eatObject: and do) or even think about messages etc, it stops making sense. You're not telling the monkeys to 'do eat banana!', you're telling a monkey do to eat a banana. What's a [monkeys do], and what happens if you use it outside of that nice-looking [[monkeys do] eatObject:banana] statement?

Well, sure, I know that it's a trampoline, and that I could do something like:
    
trampoline=[monkeys do];
[trampoline eatObject:banana];
[trampoline swingInTree:[[[NSCFTree tree]];
        //oh, I wish there were such a thing!
[trampoline goToSleep];

but that's implying that trampoline is an object which could be called a 'do' or at least, the monkeys' do. I guess my point is that the method name should be a noun rather than a verb, since it returns something, and we're sending a message to that something, and somethings are usually represented by nouns. I don't know which noun, but it should be a noun. Like guyWithWhipWhoMakesThemAllDoStuff or massMessageSender (also known as spammer.) They're pretty stupid names but it sure beats playing with monkey do.

Ooh, I have an idea! Call it a dispatcher (or is that name taken?) since it dispatches messages to the object or objects it represents. A dispatcher for a single object (i.e, not a collection) would be sort of like an NSProxy in a way, although that's completely unrelated to the name for dispatcher, it's just perhaps made more obvious by it. Anyway then you could have the object called a dispatcher rather than a trampoline, and the message would also be 'dispatcher' rather than 'do'. It would be more consistent, and dispatcher is a noun.

-- AngelaBrett

I like the fact that you are thinking about naming, because I think nameing is one of the most important things we do.

So it turns out I didn't call it Higher Order Message without reason.  Messages like -do, -collect, -reject etc. aren't normal messages, they are higher order messages.  They are messages that take other messages as arguments, not objects.  Therefore, the analogy with the message naming the object it returns does not apply.  The fact that these types of messages do, in fact, return an object is just an implementation artifact, and one that I think is being dwelt on way too much.  A HOM aware language/compiler could in theory just build the invocation directly, without all this bouncing messages around in trampolines.

You can think of the higher order messages a bit like modifiers, additional syntax that alters the meaning of what follows.  So in that sense, higher order messaging really is an extension of current OO syntax and semantics, even though we can implement it with what we have available.  Another way to think of it is that these modifiers allow you to specify your own messaging semantics, which in perfect recursive meta-reflection is implemented using objects and messaging.

The decision to use the Smalltalk-ish names, by the way, came at the last moment when I was just about to release, and it turns out that the original Smalltalk names were also just a joke...so finding better names may be a good idea.  I am somewhat partial to "forall" or "foreach", possibly with the indicator of what to do being separate.


--  MarcelWeiher

You deleted my response! Oh well. In any case, dispatcher would be alright since it dispatches, but I still prefer trampoline because the traditional use is to send messages right back; i.e. to bounce them. array dispatcher] eatObject:banana] looks ridiculous to me in comparison with -do; your mileage may very well vary.

-- [[RobRix

Oh... there was a response? Sorry, I didn't see it. :( Does this thing have any sort of protection against two people editing an entry at the same time?

I think [trampoline eatObject:banana] looks marginally more ridiculous than [dispatcher eatObject:banana], but my real point is that a 'do' isn't an object, unless it's doggy do.

Hey, look, there's history! Well, to make up for my gaffe, here's what RobRix said which I deleted:

"-do loses some of the coherency of the messages you're used to seeing, but you gain readability if you ignore everything you just said :) 
Seriously, monkeys do] eatBanana] makes sense to me even if it's not classic [[object variable] anotherMessage] syntax. And also, it does match +alloc as in [[Class alloc] init]. 
Whether or not you want to assign the trampoline is a matter totally other. I'd rather call the intermediate object something else: 

    
[[LSTrampoline *monkeysProxyThing = [monkeys do];
[monkeysProxyThing eatBanana];
[monkeysProxyThing suggestThatAngelaLiveWithThis]; // :)


This is the price you pay for trampolines, I think. I don't consider it a drawback in the least."

-- AngelaBrett

And now here's what RobRix said which has yet to be deleted :)

I don't think there's much in the way of protection against that, no; I don't think I've ever seen an entry get corrupted but the most recently saved will win, I think. As you've discovered, the history function is useful.

[trampoline doSomething] is indeed more ridiculous, but then you might as well call it monkeyDispatcher since that's just a variable name--the class itself should probably remain a trampoline, as these things are in use in other circles as well, I gather (something to do with gcc. I knew this once). But I'm not sure how often that would even come up! Unless you're doing a few -do calls in a row, there's no benefit to using an intermediate variable--and keep in mind that the equivalent of this in "regular" code would be a number of for or while loops. For me, it's yet to come up.

-- RobRix

Sure, there's no actual benefit in the code I posted, but you might put the dispatramptcholiner into a variable in order to pass it to another method. Hmm, I'm not sure when you'd want to do that now I think about it. I was going to say 'To the other method the dispatcher would be almost indistinguishable from whatever kind of object it expected to receive, so you could get the method to do its thing on several objects at once without realising it' but then I realised that there would be a big difference if the method needed to get a return value from a method on the dispatcher.

Aah... no there wouldn't be! There was mention that the trampoline would return an array of the return values. Well, instead of that, how about it returning a trampoline for the array of return values? Then maybe it would be able to work just like a regular object because the return value could be used as though it's one object, unless you wanted a scalar return value. It might get messy if something tried to use the return value (or the trampoline itself) in, say, a set: method, too. It could also get tricky if there's more than one kind of object involved... how do you respond to -respondsToSelector: if some of the objects do and others don't? I should probably look at the implementation.

-- AngelaBrett


I'd have to come out as against the idea of implicit trampoline-making; the point is to have a collection you can do anything with, not just a proxy to the objects it contains. So unless your trampoline decides to start bouncing -objectAtIndex:, -member:, et cetera to the array but not its contents, you'd be stuck. In short, if you want to do HOM with the returned object, do it explicitly!

    
[[array collect] resultsOfSomethingOrOther] do] logThyself];


-- [[RobRix

Nice discussion here.  This bit:

*They're pretty stupid names but it sure beats playing with monkey do.  *

*very* nearly made me spit Coke all over my keyboard  ;)

Anyyway, something else occurred to me:  what if this "trampolining" could be done through a separate object, but as a category on NSArray?  Then this:

    
monkeys do] eatObject:banana]


would become this:

    
[monkeys eatObject:banana]


and by extension, this:

    
[[[[array collect] resultsOfSomethingOrOther] do] logThyself];


would become this:

    
[[array resultsOfSomethingOrOther] logThyself];


This will only really work as a category if [[NSArray doesn't already implement the forwarding methods, of course.

One potential drawback would be that any selectors that NSArray normally responds to would be impossible to send to the collection (unless we also put a "do" method in our NSArray category that returned a separate trampoline object).  

Also, the syntactical division between normal message and HOM is blurred with this approach.  I'm not sure whether this is good or bad.

-- JackNutting

There's a big benefit to having transparent collections, but it might be better to write your own NSArray subclass to do it rather than try and hack Apple's one.

I'm curious, RobRix. How are you altering the profile of the message you're sending?
    
-(id<protocol>)myMethod;

id<protocol> ret = array do] myMethod];
[ret doSomeProtocolMethod]; // N'u-uh


-- [[KritTer

If blurring the syntax between single instances and collections is your goal, you might as well implement the HOM methods on NSObject as well and override those implementations on the collection classes. Otherwise, why bother with trampolines at all?

You could write -forwardInvocation: on the collections to forward messages to the contained objects, but straight trampoline HOM is conceptually simpler--and it's already done :) But like KritTer, I'd suggest doing this on a subclass rather than on NSArray itself.

As for your question, KritTer, I dunno what you mean. And -do doesn't return anything anyhow... . Really not sure what you mean.

-- RobRix

*plus you list a protocol in <...>, not a category* Oh, yeah, silly of me. It's been a while.

What I mean is, what does array do] myMethod] actually return?

-- [[KritTer

Oh... okay. As far as I know, it returns void in an id-sized container :) Honestly, I have no idea. But you will have immediate problems. And the compiler will bug you if you try to assign from a void-typed message (which you won't necessarily be using, but might be).

-- RobRix

I can tell you what BSTrampoline would do - I can't say about LSTrampoline because I haven't looked at it closely enough (*is lazy*).
array do] method] would return the result of [[array lastObject] method], with, indeed, the correct return type. That, however, is an implementation detail. But not one that's likely to change.

Actually, there's no need to be _that_ lazy, I'll go look at [[RobRix's version.
It would /probably/ return a pointer equalling 0 (so 0x000000).. don't rely on that without testing it:)

-- ThomasCastiglione

Not a good idea to do array do] copy] then :) -- [[KritTer

No matter what -do returns, no; the documented spec. says that -do returns nothing at all, so trying to use the nothing it returns would be quite silly of you, even if it *is* something. -- RobRix

But I thought -do returned a trampoline... and you're sending the [no]thing it returns a message so it had better be something. -- AngelaBrett

Kritter's snippet would be fine - what you shouldn't do is [array do] copy] something]. As [[AngelaBrett says, -do returns a trampoline; [aTrampolineMadeWithDo something] returns nothing at all.

-- ThomasCastiglione

KritTer's snippet would **not** be fine, since you'd be leaking every copied object. -- KritTer, talking in the third person

Thank you, KritTer, exactly what I would have said in the second half of my comment if I'd remembered to write it. array collect] copy], on the other hand, is perfectly fine, if you assign to something and otherwise don't leak memory like a sieve.

*shudders at the thought of [[KritTer's snippet being performed where "array" contains a series of bitmaps or other large datasets*

-- RobRix

Point taken. It wouldn't *crash* though :) Not until it had been run a few thousand times and filled up the hard drive with VM pages, at any rate.-- ThomasCastiglione

Yeah, but the brazen bugs are always the easy ones. I'd rather have one of them than a schroedinbug any day. -- RobRix

So if I'm understanding this correctly, neither [array do] nor [array collect] actually return the correct type for whatever method you then send them? -- KritTer

Neither, indeed, do [array select] or [array reject] - they and [array collect] return NSArrays (for whichever method you send them). That's the way it's meant to work, though - you're getting an array of the collected, selected or rejected results. --ThomasCastiglione

It's fun discussing what do doesn't. :) As I understand it [array select] [array reject] and [array collect] all return trampolines. But array select/reject/collect] someMethod] returns an array of the selected, rejected or collected results. It looks nice at first but when I think about the semantics too much I don't like it (see above.) -- [[AngelaBrett

That's funny, Angela, the more I think about the semantics, the more I can't live without HOM. And yes, all the HOM methods return trampolines, but since the point is for those to be transparent enough that you only ever work directly with the results of sending a message to the returned trampoline, and those results are determined by the initial HOM method, you can say that -collect results in an array even if it doesn't return it directly.

To be perfectly clear: -collect, -select, and -reject all result in arrays. -do results in something (at least, I think it does), but it's not clear what that something is, just that it doesn't hurt anything unless you antagonize it. You can, of course, say that -collect:, et cetera (note the colon) return an array, but of course you don't call them directly anyhow. I suppose you could do so, if you wished. But anyhow.

-- RobRix

----

This is all really quite nice.  I have a thought though:  As far as I can tell there is not yet support for iterating over arguments instead of message targets.  For example, in FScript, you can do
    
> 'hi ' stringByAppendingString:@{'neighbor','earthworm'}
{'hi neighbor', 'hi earthworm'}

It seems to me that the same approach should work for this as worked above, with some different names (I don't know the smalltalk names).  Perhaps
    
@"hi" collectEach] stringByAppendingString:[[[NSArray arrayWithObjects:@"neighbor", @"earthworm",nil]]

     -collectEach would return some object - not a trampoline I guess? - which is smart enough to gather up the results.  Any thoughts?

-KenFerry

[Interjection:  actually, as I keep pointing out, there *is* support for iterating over arguments in the original implementation of HOM, MPWFoundation.

    [@"hi"] collect] stringByAppendingString:[[[[NSArray arrayWithObjects:@"neighbor", @"earthworm",nil] each]]

-- MarcelWeiher 

]

EDIT:  Okay, how does this look for a start?  It's based off BSTrampoline ONLY because I'm new to this, and I thought the shorter BSTrampoline might be easier for me to wrap my head around. 

This is not remotely working code, more trying to think about what's the right thing to do.

I was about to type "put the following in a category on NSObject", but I bet that isn't safe, because NSObject probably implements forwardInvocation? Or is forwardInvocation a language feature, and thus not NSObject's responsibility... hmm.  If it is a problem,  maybe a subclass of NSObject posing as NSObject, and then forwarding the invocation to the real NSObject if it isn't handled?  Is that possible?  Anyway,

    
- (void)forwardInvocation:(NSInvocation *)anInvocation
{
    for (i = 2; i < anInvocation methodSignature] numberOfArguments]; i++)
    {
        id *trampoline = [anInvocation argumentAtIndex:i];
        if ([trampoline isKindOfClass:[[BSTrampoline])
        {
            [anInvocation invokeWithTarget:trampoline];
        }
    }
}


Then BSTrampoline needs another bunch of modes, such as kCollectEach or something.  Our code is triggered like so:
    
NSArray *suffixes = [NSArray arrayWithObjects:@"neighbor", @"earthworm",nil];
NSArray *strings = [@"hi "  stringByAppendingString:[suffixes collectEach]];

This hopefully yielding the array      {@"hi neighbor", @"hi earthworm"} .

PS - Nah, forwardInvocation has to be a language feature, right?  There shouldn't be a problem putting it in a category on NSObject.

Further comment:  I see that this sort of thing is implemented in MPWFoundation already, with slightly different syntax, so I'll probably use that implementation and stop working on this.  In MPWFoundation, the call

    
NSArray *string = @"hi " collect]  stringByAppendingString:[suffixes each;

has the desired effect.  It's maybe not quite so transparent to read, but I'm not sure the collect statement belonged way on the inside anyway.

----

-forwardInvocation: is NOT a language feature per se. It is implemented by NSObject. For one thing, it uses NSInvocation, something which is not a part of ObjC, but of Cocoa. Remember always that ObjC != Cocoa.

As for me, I like the *idea* of iterating over arguments, but the method by which it works bothers me a little-- it has to poke at the arguments themselves instead of just wrapping the lot in an invocation and tossing it along. In other words, I'm too lazy to think up a really good way to implement this, and I haven't needed it yet, so I haven't had a reason to stop the laziness.

Seriously, it seems a little bothersome to me. Rather than passing an enumerator, for one thing, I'd rather pass some custom-made marker object-- it could still be returned by -each-- on the off chance that something might conceivably want a real enumerator and I might conceivably want to use that method with HOM. For another thing, I would be bothered by the necessity of checking each argument-- you could write off varargs methods, of course, but still...

All of this is to say that if anybody wants to extend LSTrampoline with this, I'd be thrilled (: I've just not bothered yet.

-- RobRix

----

Okay, thanks, you just helped me focus in on my confusion: message forwarding is part of the runtime, but the actual calls to forwardInvocation are made by cocoa classes.  Regardless, there's no problem, NSObject doesn't have a forwardInvocation method that it's unsafe to override; it just fires     -doesNotRecognizeSelector: (now I looked it up. :).

I (now) don't think the trampoline needs to be changed.  The new code, if someone were to implement it, belongs outside of the trampoline object (if it's a general object like yours).  What I'm talking about is an application of a trampoline's ability to bounce messages around.  The trampoline here is the method of implementing the goal, which is nice iteration abilities (well, that's **a** goal anyway).

As far as needing the functionality, after reading this the first time I was all happy, downloaded (BS)Trampoline, and the very first time I tried to use it I needed to iterate over arguments!  D'oh! 

Oh, as far as poking at the arguments a) that's outside the trampoline, and b) the necessity of doing so is an artifact of an assymmetry in function calls in objective-c (and most object oriented languages):  the first argument, the target object, is more important than all the others.  And I guess c) if you use the syntax that Marcel does, you don't have to poke (I think).  :)

-KenFerry

----

Just wanted to let everyone know that i've rolled my own HOM implementation. Anyone interested can get it at http://www.dpompa.com.

-Ofri Wolfus

----

I've also rolled my own for the fun of it, but mine is a little bit different. The code is much simpler than the other HOM libraries I've looked at, and I've taken different approaches on a few thing. It can be grabbed from here: http://notahat.com/nhcollections

-Pete Yandell

----

I've been thinking about HOM again, especially in the context of using it as an analog for blocks (only internalized), and, being unsatisfied with LSTrampoline, I decided to make another HOM system. I wanted to see if I could improve upon it at all, and wanted to revisit the subject of (for example) subclassing NSMethodSignature instead of using private message calls (doesn't seem to work).

After a brief brainwave when I realized that all the type-related madness run into with NSInvocation when you're trying to do -select, -reject, or other mechanisms that deal with boolean messages-- for example,     array select] isKindOfClass: [[[NSArray class]] is actually introduced with NSInvocation...

So, using the lower-level     forward:: and     performv:: skips lightly around all that, and thus was born MonoHOM.

-- RobRix

----

Since I can't comment here (the page has reached the max size), I've replied at HigherOrderMessagingDiscussionPartII. --OfriWolfus
 Pour vous inscrire  garder le  numéro, vous aurez  pu   compte  opérateur d'identification  (code RIO ) [http://obtenir-rio.info rio bouygues]. Vous obtiendrez  êtes certain d'obtenir  pour  gratuit  par  téléphonant   du serveur ou du service à la clientèle  satisfaction client  du   fournisseur de services  [http://obtenir-rio.info/rio-bouygues rio bouygues] . Vous ne  CAN   get un SMS  utilisant votre. Avec  du  [http://obtenir-rio.info/rio-orange code rio orange], alors  vous êtes capable d'  sur le  offre de  de son  ON   rouge.

