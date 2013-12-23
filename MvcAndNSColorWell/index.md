---
layout: page
title: MvcAndNSColorWell
---



After a long detour into Java-land, I'm back programming on the Mac but am really not getting the Cocoa paradigm.  Here's a simple example:

I want to model a color and present it to the user for possible editing.  I create a window with (1) an NSColorWell and (2) a static field into which to write the RGB triple as text.

Okay; this constitutes my view.  I take an NSColor; that's my model.  For the controller, I create a class with outlets for the color well, the static field, and the color, and can connect them up in IB.

But what now?  In Java, I'd make the controller implement the NSColorWellListener interface and register it for changes in the color well.  Similarly I'd make it a NSColor listener.  Listeners are the vias by which information flows.

But Cocoa doesn't work that way.  How does my controller know when the user has clicked in the color well and chosen a new color in the color wheel?  Or if the user drags&drops a new color into the well?  How do I set up the controller to recognize changes in the NSColor, the model?

Being a control, the color well can be set up programmatically to send a message X to a object Y at some time Z.  But you can't set that up in IB, can you?  Do the paths of communication need to be done outside of IB?  Programatically?

I've been reading documentation all day and I'm not getting this fundamental idea.  (It's not quite down to M$ level yet, but Apple docs have plunged in thoroughness [and accuracy] since the golden era of Inside Macintosh.)  

Any clarification and/or Cocoa idioms appreciated.

-E

----

Set the color well's action to an action method in your controller.

And read some tutorials. This is extremely basic stuff, and you'll get a much better answer to all of it from a tutorial that's specifically geared towards beginners. Cocoa is different from *everything* else out there, so don't expect to be able to succeed without spending lots of quality time with tutorials, books, and docs just because you have lots of experience with other things.

*BTW, the equivalent of a "Listener" is a Key-Value Observer. Check out docs on both KeyValueObserving and CocoaBindings. --JediKnil*

----

Actually, I've found most of the tutorials useless because Cocoa reinvents the wheel in a lot of non-standard ways.  Here's a quick attempt at translating into standard (i.e., Design Patterns) terms.  

The Observer pattern is implemented in Cocoa, but only *one* observer is allowed.  This is the "target" of the control, and the message it receives is the "action" of the controller.  InterfaceBuilder does this graphically by drawing arrows. I've found that tutorials usually go light on how to do it programmatically.  Note that while in Java the method that is called on changes is fixed (by the XxxListener interface--it's usually the only method), in Cocoa it can be any method you want.

In your case, set a component as target for the NSColorWell and choose the action: that's equivalent to registering as a Listener and the action you choose is equivalent to colorWellChanged().

Now, I was fibbing in the paragraph above: you actually *can* set up more than one observer at a time, but you must use a *different* mechanism: KeyValueObserving as mentioned in the earlier post.  InterfaceBuilder will allow you to set up observation via CocoaBindings for certain supported classes, but if you want to set it up graphically for an arbitrary class, you have to extend InterfaceBuilder or you're out of luck.  You can always write some code to do it programmatically.

If you're doing it programmatically, you register for changes with NSObject's addObserver:forKeyPath:options:context:.  You'll get called when the field with the given name (key) is changed (but only if they change it in a certain manner (setValue:forKey:)).  All notifications go into the same bag--willChangeValueForKey:/didChangeValueForKey:--and you have to switch on the field name (key).

The result is logically equivalent to Java, but wildly different in appearance.

Cocoa heavily optimizes simple cases (like the single Observer), but then underdocuments and makes much more complicated the complex cases.  If Cocoa's your first language, this'll be great because you can write easy programs quickly (especially with IB) and you can progressively learn how to code more complex programs.  For me, coming from other more uniform languages where simple cases aren't optimized for, it's been painful to learn the Cocoa Way(s) of doing things, even standard design patterns.

----
It is informative to note that many of the famous design patterns in "Design Patterns" were first recognized in NeXTstep, a precursor to Cocoa, and in fact "Design Patterns" provides many such citations.  It is odd in the extreme to write that Cocoa reinvents the wheel because many of the patterns exist in a "pure" form within Cocoa except that the Gang of 4 renamed them for the book.

Regarding the Observer Pattern: You might find NSNotificationCenter interesting.

Regarding the Target/Actions pattern: This is not just an optimization of a common case.  It is absolutely essential to understand the Cocoa Responder Chain (The Chain of Responsibility pattern) and its interaction with the Target/Actions pattern.  Target/Action is a dynamic and flexible replacement for the Command pattern that is frequently implemented in staticly typed languages like Java and C++.  The Cocoa equivalent of the Command pattern is seen in NSInvocation, but it is very seldom used.

Setting targets and actions is graphical in Interface Builder and works equally with custom classes and framework classes.  I can't remember the last time I wrote code to call -setAction: or -setTarget:.  KVO/KVB are relatively new techniques that can be used in many cases where Target/Action can be used, so there is some overlap.  Neither technique fully replaces or supersedes the other.  KVO/KVB may also be set in IB, but the current integration with IB makes its use with most custom classes less than ideal.

Finally, many newcomers to Cocoa complain that they "can't" do such and such with code or that the documentation is scant regarding doing things programmatically when what you want to do is normally done via Interface Builder.  Rest assured, Interface Builder is not doing anything that you can't do in code.  Interface Builder calls the very same methods that you can call (and no others) in response to user interaction with IB.  Once the state and interconnections of the objects are set (via IB calling methods in response to user input), the entire object graph is archived (serialized...another renamed pattern) into a "nib" file.  When a nib file is loaded into your application, the objects (complete with their state and interconnections) are un-archived and resume operation just where they left off in IB.  This is a very elegant system.

A smart person once said that every line of code is a potential bug, and every line of code potentially requires maintenance at some time in the future.  The fastest, cheapest, least bug prone line of code is the line you didn�t have to write!  Interface Builder is a tool that is used to replace hundreds or thousands of lines of code with a data file.  Interface Builder is one of the keys to Cocoa�s famous productivity and quality.

