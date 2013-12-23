---
layout: page
title: AreObjectiveCTwoPropertiesUgly
---



Maybe this discussion has raged elsewhere for days, maybe I'm just resisting change, but I do find the new Objective-C 2 @property implementation to be ugly and confusing. I think it makes a mess of a fine, simple and elegant language.  Do you agree or disagree?

I've read a few articles on the new Objective C 2. I like the small changes they've made in the most part (arguably, Garbage Collection isn't a small change, but has been implemented in such a way that it's a small change to me). On the other hand, the new @property implementation is difficult. If you read Scott Stevenson's two articles [here: http://theocacao.com/document.page/510 and here: http://theocacao.com/document.page/516] he takes a long time explaining this new feature. Any new language feature which needs two long articles to explain it surely must be a mistake!

We all know that there must been an easier way to add properties to classes than what was provided in Objective-C 1. I myself have been using a couple of pre-processor macros for years to stop me having to type in all that nonsense. But introducing a number of new keywords and a completely new Objective-C syntax for accessing properties is something which really needs to be "done right". I'm not sure I feel it has. For example, I don't think there should ever be "two ways to do something" - it can lead to sloppy, inconsistent and buggy coding (yes - that's you, my scripting language friend). So when I see an example like this:

    
 NSString * director = movie.director.fullName.capitalizedString;


which is equivalent to this in Objective-C 1:

    
 NSString* director = [movie director] fullName] capitalizedString];


It upsets me (!) that the capitalizedString part is being called as if it were a "property" whereas in fact I see it as an [[NSString method. This new notation it isn't simply about properties: you can also call methods using this notation, so the new @property features have "creeped" beyond their remit. Instead, I want to see the Objective-C 2 example look like this, any other way should result in a compiler error:

    
 NSString* director = [movie.director.fullName capitalizedString];


This would be a much more consistent way to add properties to the existing language.

Beyond muddying the purity of Objective-C, the implementation can be just laughable when you're talking about private assignment methods. Just look at the section "Public Properties with Private Setters" of Scott's second article, in which you need to do several twisted contortions to get around compiler warnings and errors. I think I may stick to my preprocessor macros.

I can understand that there may have been compromises to get backwards compatibility. But I wanted to see something which extended the elegance of Objective-C 1 to include properties, whereas what I think we've got here is something which just adds a hack worthy of a badly designed scripting language. Maybe I'm over-reacting, maybe I'll get used to it, but if there's anyone else out there who feels slightly dirty typing in @property and @synthesize into their Objective-C code, let me know and we'll start a support group, thanks - DavidThorpe.


----
I think properties make code much cleaner. Just use them wisely. 
----

Actually, I think they haven't gone far enough with the new property syntax.  I think that I should be able to use any keypath I want.

    
 musicLibrary.tracks.@distinctUnionOfObjects.rating = 5


Either that or they shouldn't have introduced it at all.  --K

----

*Are they ugly?*: Are you serious? 

*Either [ my way ] or they shouldn't have introduced it at all.*: So because it's not implemented with the extras (that are part of the Cocoa KVO/KVC mechanisms, not the Objective-C language itself) *you* want these otherwise *incredibly useful* and time-saving shortcuts for common patterns have no value and shouldn't have been introduced?

The conceit on this page is as comical as it is astounding.

----

I was referring to the accessor syntax, not the property concept itself.  Properties are one of my favorite features in C#, and I'm glad Apple have implemented them in ObjC.  But I think that the new syntax is absolutely unnecessary unless it allows us to do things we couldn't do before.  Being able to use the full keypath expression as an lvalue would be a reason to implement the new syntax (because it would be confusing to use a message as an lvalue).  But as it stands, foo.bar is just the same as [foo bar], which I think is unnecessary and confusing.  --K

----

This is what I'm saying too: I think properties are necessary but I don't think they've been implemented in the right way, and I too prefer the C# approach. Perhaps they could have been added within the instance variable  declaration parts in the .h file:

    
 @interface Model : NSObject {
   @property (copy) NSString* summary;
   ...
 }
 
 ...
 @end


Why bother with the @dynamic or @synthesize parts at all? Properties could then be exclusively accessed using the dot notation, and method calls by the square bracket notation. It's completely backwards-compatible, cleaner and isn't the strange half-way house that the current implementation seems to be. I'm genuinely interested in a discussion on the Objective-C 2 implementation of @properties, not trying to be inflammatory, thanks - DavidThorpe
----
    @dynamic and     @synthesize might be artifacts of GCC, and therefore necessary.  And to be honest, I'm not entirely sure that divorcing ivars from methods is within the spirit of the language.  Regardless, I think the nature of Objective-C lends itself to a better implementation through conceptual harmony.  If keypaths (and, for that matter, predicates) could be evaluated as lvalues (references like in AppleScript), ObjC would be that much more awesome.  Think of doing something like     NSEvalPredicate(musicLibrary.tracks.rating == 3).rating = 5.  Would sure give C#'s LINQ a run for its money. --K

----
    @dynamic and     @synthesize are just in keeping with the tradition of C, where you always have separate declaration and definition. By declaring a property you just tell other people that this property exists, but not how it works. Then you implement it using either     @synthesize to tell the compiler to make it for you, or     @dynamic to tell it that you'll handle the details yourself. It fits in pretty well with the rest of ObjC in this respect.

As far as keypaths being lvalues go, I thought that was allowed. The dot syntax for accessing properties isn't really tied to properties, it just uses KVC, or so I thought.

----

I see your point about the need for     @dynamic and     @synthesize and keeping with the spirit of C, but why do we need to generate any code for property setting and getting at all, can it not be part of the objective-c runtime? Properties are more like ivars which don't have any corresponding definition. I suppose if we need to override the default behaviour, perhaps there could be a way to do this. But in general, in my humble opinion, all that should be required is a @property declaration in the interface definition - DavidThorpe

----
I really do mean arbitrary keypaths.  And adding predicates on top would be icing on the cake. --K

----
Sure, but as long as your arbitrary keypaths are valid, in that each key along the path returns a KVC-compliant object, wouldn't they work?

----
According to the documentation, no.  Dot syntax is not transformed to KVC (e.g.     -setValue:forKeyPath:), just straight to     -setFoo.  But the getters and setters generated are KVC-compliant, so you can use KVC to manuplate properties.  Also, you can't use operators like     @distinctUnionOfObjects in property syntax. --K

----

Why not make every NSObject KVC compliant, or every object an NSManagedObject rather than an NSObject? Anyway, it appears history is repeating itself a little bit, this very topic was discussed before here: http://www.stuffonfire.com/2006/12/08/does-objective-c-really-need-properties/

----
Why not make every NSObject KVC compliant? Every NSObject *is* KVC compliant. KVC will automatically set and retrieve instance variables which correspond to key names, with no other intervention required.

Why not make every object an NSManagedObject? Total overkill and inefficient. Most objects don't need it, and the overhead of having NSManagedObjects around for every little string, boxed number, dictionary, array, etc. would be killer.

----
Perhaps coincidentally, perhaps not, Mike Lee just addressed this topic in passing, as well as a bunch of other shortcomings of ObjC, on his blog today: http://www.atomicwang.org/motherfucker/Index/57801B38-03B2-4202-A3FE-45FA1078A538.html

I think ObjC 3.0 could use some radical (and breaking) changes for 10.6 or 11.0 or whatever release is next.  Overhaul the ObjC language, switch to llvm-clang, completely deprecate Carbon, and everything is 64-bit.  That would be a true revolution. --K

----
I doubt they'll be able to make certain of those changes-- deprecating Carbon in particular. It would lose the platform both Adobe and Microsoft, as I doubt either of those is likely to be moving their entire development workflow to another language and set of frameworks.

----
Well, they've already abandoned 64-bit Carbon, which I'm sure Adobe is none too happy about (unless, of course, they have a special arrangement with Apple...), and they keep building bridges from Carbon to Cocoa.  So I'm not entirely sure that deprecating (not removing, mind you) Carbon is entirely unfathomable.

