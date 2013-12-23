---
layout: page
title: MultipleInheritance
---




Multiple Inheritance (MI) is an ability of some programming languages -- notably C++, but not ObjectiveC -- to define classes which inherit from more than one SuperClass, basically merging the abilities of the two and adding its own to the mix.

As stated, ObjectiveC does not have this, but provides for similar abilities with Protocols.

----

The debate has been going on for a long time about whether or not MultipleInheritance is a good or bad thing. The makers of Java thought it a bad thing, the makers of ObjC thought it a bad thing. The makers of Python (correct me if I'm wrong), Ruby and PHP thought it a bad thing.

[Correction, you're wrong. Python do support multipleInheritance. It's rarely used however due to the complexity of the super call which is not understood by the vast majority of python developers.]

The thing is that with FormalProtocols, InformalProtocols, categories and delegation multiple inheritance is unneeded and will only add complexity to the language. Therefore ObjC does not allow multiple inheritance.

----

I've never seen a good piece of code that relied on multi-inheritance.  Once objects become more complex multi-inheritance does not work as advertised.  There are always problems with it unless you are creating something extremely simple like creating a new textbox that inherits from two separate textboxes where one textbox has a FONT set to courier and the other textbox has a maxlength set to 10 with the resulting NEW textbox having the characteristics of both.  All sorts of problems begin to popup when the two objects share some of the same settings made to identical properties, especially when they both share methods that are named the same but produce different results.   ---> K. McClanahan

----
I take it you've never used the standard template library then?

----
Metrowerks' PowerPlant had some nice use of MI with their mixin classes.  The textbox strawman presented above is just a sign of a very poor design, not a deficiency in multiple inheritance itself.

----

I'm not sure that I'd agree that Java and ObjectiveC designers think that multiple inheritance was bad or evil. They believed that multiple inheritance added some significant complexity to the compiler and language rules. They opted to use protocols/interfaces to give the developer similar functionality - with some perks that multiple inheritance does not provide. Yes, I've seen multiple inheritance used where other language tools would have been more appropriate. Of course, that can be said of overloading, overriding, inheriting, delegates, events/notifications: just about any language feature. -- TimHart

----
My experience is that MultipleInheritance is usually the sign of bad class hierarchy design.  That said, NSButtonCell, ought to have inherited from NSButton and NSCell, instead of performing some peculiar ivar contortions that I wouldn't want to try at home to accomplish the same thing.  GUI is most often the place were MI is appropriate, and its absence is sorely missed.

----
Your statement about NSButtonCell seems to belie a lack of understanding about the NSControl/NSCell system. Making it inherit from both would require re-architecting the entire framework; NSCell is *not* an NSView, and intentionally so; it's a low-overhead encapsulation of the basic drawing and behavior of a type of control, which can either by standalone via the corresponding NSControl subclass, or be embedded in a larger control like NSMatrix or NSTableView without the performance hit that having a lot of embedded views would have. Using MI in that situation wouldn't make any sense.

----
I disagree.  The use of protocols and MI combined with some overriding would be ideal.  You need not inherit what you don't want.  Mouse tracking and some button drawing routines (not drawRect:, though) are methods you probably *do* want to inherit.  If you don't you just have to cut copy paste, unless you made you drawing routines functions, or class methods, which is just lame.  So, I think you comment seems to belie a lack of understanding about MI :).

----
In MI as in single inheritance you can't choose which methods you inherit, you inherit them all. You can choose which to *override* but that's very different. In most languages there is no such thing as selective inheritance.

The point of not making NSCell an NSView is that NSCells are supposed to be lightweight, if it inherited from NSView they wouldn't be, and the whole drawing system would bog down.

----
Duuuuuude... notice I say "some overriding".  You override and don't call *super* or *that* or however your language refers to super classes.  In many implementations of MI you can specify **which** super class you want, and in doing so you can bypass the heft of NSView, but retain the desired functionality from NSButton.  Without MI, you have to agree that the code is either horribly redundant (there are two implementations that do the same thing) -- OR, just as bad, they've resorted to calling functions to do some of the shared functionality... which is not very cool.

----
Actually, it's neither.  An NSButton view just uses an NSButtonCell to do its drawing and click tracking.  When you have one object that needs to include the functionality of another object, 9 times out of 10 you should forget about inheritance and just make the second object a member of the first.  -- Bo

----
Yes, Bo, that is what I meant by "peculiar ivar contortions"...  I don't think that making NSButtonCell a member of NSButton is the optimum way to proceed.  For one, NSButton loses access to NSButonCell's ivars, which adds some unnecessary overhead -- and saving on overhead is the whole reasoning behind having a ButtonCell not be a subclass of NSView...  The absence of MI has never been an impasse -- I have not once in my life come across a case where there was something I could do with MI that I couldn't so with SI.  However, I still hold that MI would produce a cleaner solution in the NSButton/Cell case.  That said, I think the times when MI clarifies as opposed to obfuscates are far and few between.

----
It is quite odd to hear putting one class as a member of another described as "peculiar ivar contortions";  that probably describes 99% of my classes.  -- Bo

----
Wouldn't having the cell as a mixin rather than MI be better (so that the view doesn't have to transfer so much state to the cell, and the cell could actually catch responder methods etc.)? But I don't think it works overall, because often one class may have multiple cells, re-using cells for different portions of the view etc.

With respect to MI, in C++ MI is really only successfully used to declare interfaces. I.e. you inherit from several abstract base classes, where each really denotes an interface (protocol).

The advantage over Java Interfaces is that you can inherit code.  Another reason might be purely syntactic, e.g. rather than having to go through allocator.alloc() and allocator.free() in all of the methods of mine where I need to call these methods on allocator, I MI from the allocator class -- but personally I never do these kinds of things...

----

From an ObjC perspective, I think it would be nice would be a way to tie method implementations to protocols.  So for example, to conform to a protocol you'd have to implement 2 methods, but you'd get 10 more methods 'for free'.  So for example, instead of the current NSArray class cluster, you'd have

    
@protocol NSArray
- (unsigned)count;
- (id)objectAtIndex:(unsigned)index;
@end
// just a quick example.  The real extended list would be much longer.
@protocolinterface NSArray
- (id)lastObject;
@end
@protocolimplementation NSArray
- (id)lastObject
{
	unsigned count = [self count];
	if (count) {
		return [self objectAtIndex:count-1];
	} else {
		return nil;
	}
}
@end


As long as the implemented methods only called methods in the @protocol or @protocolinterface sections, it 'should work' and it seems like a cleaner way to implement this kind of thing than either MultipleInheritance or ClassClusters.  And heck, as long as we're dreaming, might as well allow categories on protocols too, i.e.     @protocolinterface NSArray (MyExtensions). :)  -- Bo

PS Obviously this opens up all the same problems people much smarter than me have with MultipleInheritance, and it doesn't actually make anything possible that you couldn't do before, but it just seems like a nifty semantic construct.

----
I might have the terms wrong, but I'm quite sure that *a protocol with partially implemented methods* is referred to as a mixin. Ruby uses this quite a lot, e.g. it has an Enumerable mixin which gives your class find, grep and similar methods based on the enumeration primitives that your class offers.

I also think this would be a great feature, especially for ObjectiveC, since the amount of boilerplate code I have to write is often greater then what's really required to express the functionality that I am after.

I have experimented a little with having     +initialize add methods to my classes, based on the existing methods and/or instance variables (e.g. automatic setters, getters, toggle-functions which write the ivar to user defaults etc.), but it's very difficult to make really flexible, because registering a new method for an ObjectiveC class requires a C function pointer, and there is no "user data" provided to this one, so any user data that I need to customize the C function (which needs to be written in advance for obvious reasons) must be in the form of the selector name, and that's often not under my control.

Perhaps my goals will be easier to obtain with that new managed objects coming in Tiger, but I don't have any documentation on them yet, so too early to say...

Another approach is to simply wrap the object to be extended, similar to what is done by     mutableArrayValueForKey: -- I haven't pursued that path fully yet.

Eventually I'll probably just abandon ObjectiveC and use a language better suited for meta programming.

----
The partially implemented protocol bit is just an abstract class � la Java, but with MI, so really it is just MI with the addition that the compiler will flag an error if the subclass does not provide an implementation for a specified method. Yes, from a design perspective, ObjC would benefit from abstract classes (as from things like final, protected and private for methods and classes), but I'm not sure that I'm ready for the complexity of inheriting multiple abstract classes. IMO MI is easily replaced by delegation, especially in a dynamic language like ObjC.

May I also remind you that ExtendsIsEvil. --TheoHultberg/Iconara

----
I for one, never argued that ObjectiveC should be given an MI makeover.  I disagree with Bo, though, that having NSButton retain NSButtonCell as an ivar is clean.  I *do* think it's unnatural.  There is a difference between a class having ivars in order for the class to perform it's basic functions, and "wrapping".  95% of the time I see wrapping in other people's code, it's code that has been written by programmers with little OO experience under their belts, and it's clear that they haven't fully grasped class hierarchies.  When you are forced to provide (and declare) (and document) two interfaces that have a large subset, something is amiss.  In ObjC you'll usually find that that's just the time for a protocol, but a protocol is used to define one interface with multiple implementations.  A protocol would not be appropriate for NSButton and NSButtonCell.  They are a clear instance of class wrapping, and I hold that WrappingIsBad.  That said, let me state yet again, that this is a one out of a million case where I think MI would have been good.  Cocoa does provide NSProxy, which is many ways is a fantastic substitute for MI, but NSProxy is not appropriate where speed is of the essence.  Window drawing is not something you want to spend your cycles on.

----
The NSButton/NSButtonCell situation is the standard behavior of NSControl. NSControl performs its drawing and click-handling through NSCells in order to give it the kind of lightweight flexibility that makes NSTableView and all the other multi-cell controls possible.

----
Also, NSProxy? What does DistributedObjects have to do with MI?

----
It should be noted that NSProxy is useful for far more than just DistributedObjects

----

The way I plan to get around MI problems (to use mixin-like MI) is using a single-inheritance-based reference system. This works based on the order you declare your superclasses. So in pseudocode:

    
class A { char c; }
class B { char c; }
class Combo extends A, B {
   public void myMethod {
      out.print(c); // Refers to A.c
      out.print(super.c); // Refers to A.c
      out.print(super<B>.c) // Refers to B.c
   }
}


Maybe that's not the exact syntax I would use, but the point is everything is inherited based on order: the leftmost superclass has priority. All of this stuff would be determined at compile time, so it's not like it would make runtime evaluation any harder.

All this stuff is for a language I made up (hence "I plan to" instead of "I would"), but probably won't implement for a while. There's still the small problem of "diamond inheritance," but you have to admit that sometimes it would be a lot easier if you could extend a Cocoa class and its subclass. Case in point: I used a custom subclass of NSOutlineView in my program that had identical code for the equivalent NSTableView subclass. In my case it doesn't matter, but for a framework, you would certainly rather have MyOutlineView inherit MyTableView's methods as well as NSOutlineView's. For a mathematical example, consider the square. It is a rhombus and a rectangle, yet neither of those are "subclasses" of each other. Also take C++'s IOStreams (which I myself never liked) - composite input and output streams! There are certainly cases when there are methods common to two classes, and the lack of variable declarations in protocols/Java interfaces makes it hard to keep an independent counter, or a reference to a delegate. Encapsulation can't solve this either.

MI is not just a bad idea, as long as it's used in a way that makes sense. If you are trying to combine an NSWindow with an NSWindowManager, however (a window that controls itself), you are breaking a lot of rules about how to organize a program. That's what gives MI a bad name. -- JediKnil

----

For a simple way to avoid "diamond inheritance" problems with Multiple Inheritance, look at the way it is handled in Eiffel.  Other languages could do it the same way. -- ScottSteinman

----

The right way to do MI is through Delegation.  See the paper "Automated Delegation is a Viable Alternative to
Multiple Inheritance in Class Based Languages" (http://www.cs.virginia.edu/~evans/cs655-S00/readings/viega.pdf) for a very nice implementation for Java.  Automated delegation can sort-of be done in Objective-C (at least for a single delegate object) by implementing ForwardInvocation. -- BruceFancher

----

One attempt to hack MI into Obj-c can be found here: http://www.toastedmarshmallow.com/Frameworks/ConcreteProtocol/index.html .  It does some of the things talked about earlier in this page, allowing you to actually implement methods as part of a protocol and not just define their interfaces.

It's a pretty nice idea though im not sure I'd use it as it seems a little too hackish.  The same thing could be acheived via forwardInvocation overriddes, maybe you could category a forwardInvocation override that does some nice stuffs for you.

