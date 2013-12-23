---
layout: page
title: NSPopUpButton
---

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPopUpButton_Class/

----

Related pages:

*DynamicMenuForNSPopUpButton


----
**Bindings**

In popups, the *content* defines what the collection of objects is; *contentValues* allows you to define what attribute of the content will be displayed; and the *selectedObject* obviously reflects the selection but�importantly�to the selected object rather than the displayed value. Typical bindings might be:


**content* to *popupArrayController.arrangedObjects*
**contentValues* to *popupArrayController.arrangedObjects.displayName*
**selectedObject* to *otherArrayController.selection.attribute*


----

After trying to deal, off-and-on for some period of time (it became my Holy Grail quest) with the problem of NSPopUpButton and reflecting the value of the NSPopUpButton selecting in a NSTextField in real-time, even after working on this with someone with much more Cocoa experience than I have (we are talking over a decade here and has authored books on Cocoa programming), the conclusion is that Cocoa Bindings are not appropriate, at least not at this juncture, for this task. And given that this can be coded using the old-fashioned Controller in about...oh, five minutes, it doesn't seem worth the effort. I have nixed the code that was here since, first it was mine and because it will only confuse people. I am leaving the text of the problem here. If anyone wants the code that was here, just email me at jdhouse4@cocoacoder.org. I'm disappointed that CocoaBindings is, compared with CoreData, so lacking in so many things that developers need, from decent debugging feedback (there isn't any) to the real inelegance of it all. As a comparison, look at the way we use CoreData and CocoaBindings. The experience is worlds apart, from nice to horrid. I hope in the future that those on the CocoaBindings team will work to make using (and debugging) CocoaBindings as elegant as CoreData.

----
Solution:

The solution over at http://www.meandmark.com/bindingspart1.html solved it for me!

- Henrik N

----

Original post (moved from BindingQuestions):
For those of you that are advanced Cocoa programmers and know how to use Bindings, my apologies, but I am at a loss. Another caveat; it's been a couple of years since I programmed in an NSPopUpButton. Anyway, I submitted this to Apple's CocoaDev mailing list and got back...silence. By comparison, this place is hopping, so here goes.

To demo how to make NSPopUpButtons work in Cocoa using Cocoa Bindings, I made a very simple Cocoa App called aptly enough "PopUpButtonDemo" that does nothing more that display someone's age when you select their name from the pop up button (NSPopUpButton).

There is only one Class, "PopUp". Some of the code, namely the first two Class methods in the implementation file for PopUp.h/m I copied from Malcolm's "ToDo" app Controller.h/m and then changed it. That was a bad idea because his works--mine doesn't.

Right now, on the UI I just have the NSPopUpButton on the Window because I wanted to see the list of names before I went off and made the problem more complicated.

I used an NSArrayController, PopUpController, because it's my understanding that a PopUpButton is handled like an array. The PopUpController has as its attribute the Class PopUp and a contentArray Binding to FileOwner Model Key Path "popUps". The NSPopUpButton itself is bound as follows:


* content = arrangedObjects [PopUps (NSArrayController)]
* contentValues = arrangedObjects.popUpName [PopUps (NSArrayController)]


I did not Bind to


* selectedObject to otherArrayController.selection.attribute


since there is no other object. PopUp is alone.

The NSArrayController "PopUps" is bound to 

    contentArray = popUps [File's Owner (NSApplication)]

When I run the app, I keep getting an error:

[<NSApplication 0x3136e0> valueForUndefinedKey:]: this class is not key value coding-compliant for the key popUps.

But I did create the NSArray popUps in PopUp.h. At least, in my code, I have an NSArray that is created and called...popUps!

----

Your contentArray should be bound to the popUps method in your PopUp object, not your File's Owner (NSApplication).

- Ashley Clark

----

Ashley, thanks. You are probably thinking that this is a Doc-based Cocoa app, which if that were the case, would be a solution. I deliberately created this as a Cocoa App to try to see if Cocoa Bindings would work the same in a "regular" app as it does in a Doc-based app. The answers is, as it seems so far to be the case, no.

- Jim

----

<pre>
File: PopUp.h
#import <Cocoa/Cocoa.h>

@interface PopUp : NSObject 
{
    NSArray *popUps; // !
    NSString *popUpName;
    double popUpValue;
}
+ (id)popUpWithName:(NSString *)aName andPopUpValue:(double)aValue;

- (double)popUpValue;
- (void)setPopUpValue:(double)aValue;
- (NSString *)popUpName;
- (void)setPopUpName:(NSString *)aName;
@end

File: PopUp.m
#import "PopUp.h"

@implementation PopUp
 + (NSArray *)allPopUps
 {
// !     static NSArray *popUps;
	 popUps = [[NSArray alloc] initWithObjects:
	     [PopUp popUpWithName:@"Jim" andPopUpValue:43],
	     [PopUp popUpWithName:@"Tony" andPopUpValue:40],
	     [PopUp popUpWithName:@"Summer" andPopUpValue:29],
	     nil];
     return popUps;
 }

+ (id)popUpWithName:(NSString *)aName andPopUpValue:(double)aValue
{
    id newPopUp = [self alloc] init] autorelease];
    [newPopUp setPopUpName:aName];
    [newPopUp setPopUpValue:aValue];
    
    return newPopUp;
}


- (double)popUpValue
{
    return popUpValue;
}

- (void)setPopUpValue:(double)aValue
{
    popUpValue = aValue;
}

- ([[NSString *)popUpName
{
    return popUpName;
}

- (void)setPopUpName:(NSString *)aName
{
    if (popUpName != aName)
    {
	[popUpName release];
	popUpName = [aName copy];
    }
    return;    
}

@end
</pre>

*Maybe that'll work? -- RyanStevens*

----
Ryan, I like the layout of your code better than my original version. But the problem seems to be that I have tried to see if Cocoa Bindings work in the same manner with a Cocoa app as it does with a Cocoa Doc app, with the answer being that, in as far as I can see, such is not possible, at least not now.

- Jim Hillhouse

----
I am confused what the "otherArrayController" is compared to the popupArrayController. From mmalc's To Do example (where the above code comes from), I can see where the 2nd NSArrayController comes from. What I am not sure about yet is if only one NSArrayController can be used as well, so that selectedObject is bound to popupArrayController.selection.attribute instead.

----
I have a "master-detail" style dialog in which I wanted the contents of a popup menu to change depending on the master list selection. After much investigation and experimenting, I achieved this with the following:

* Each item in the master list has an array containing its popup menu items as strings (eg under the key *popupItems*)
* Another array controller was made, with its *contentArray* bound to *masterController.selection.popupItems*
* The popup button has its *content* bound to that second controller's *arrangedObjects* (leaving "model key path" empty), and the *selectedIndex* is bound to another key in the master list


Thus the items in the master list each have their array of available options, and the index of the currently selected option.

-David Catmull

----
Note: I just found out (on 10.4.3) that if you create a dynamic menu for your pop-up button, create the menu items with a nil action or the actions applied through the NSPopUpButton setAction: method will not be called. IE:

    
	NSMenu * menu = [[[NSMenu alloc] init] autorelease];
	NSEnumerator * anEnum = self surfaces] objectEnumerator];
	[[ASurface * surf;
	while(surf = [anEnum nextObject])
	{
		id<NSMenuItem> menuItem = [menu addItemWithTitle:[surf name] 
			action:nil keyEquivalent:@""]; // If I specify an action here, trouble ensues!
			
		[menuItem setTag:(int)[surf type]];
		[menuItem setRepresentedObject:surf];
	}
	
	anEnum = [surfaceMenus objectEnumerator];
	NSPopUpButton * button;
	while(button = [anEnum nextObject])
		[button setMenu:menu copy] autorelease;
	
	[self redisplayMenus];


While an action was specified in the     -addItemWithTitle:action:keyEquivalent: call, any later calls to the NSPopUpButton -setAction: method does not work as expected, but if nil is specified as the original action, then all is well. I do not know if this is documented behavior, but it is confusing, and I spent too much time solving it!
-JeremyJurksztowicz
----
Even if it's confusing, it's a feature, not a bug. This way you can create menu items that all have different actions, even from the popup button. For example, this way you can hypothetically have a Back button in a browser that sends a     back: action when clicked, but a     goto: action when a menu item is selected. --JediKnil

----
Hi, Does anyone know how to programmatically create an NSPopUpButton that's small?  In IB you can choose 3 sizes, along the lines of normal, small, and mini?  I'd like mini but I can't find any function in either NSPopUpButton or NSButton that involve setting the size style.  Does anyone have any advice?  (Incase it's unclear I don't want the button to be just small by setting its size with an NSRect.  There are different kinds of sizes along the lines of large and small scroll bars.)

*Look in the cell, there are many settings which can only be set in the cell, not the control.*

I checked out its cell, and all the parents of that cell and still didn't notice anything pertaining to size style.  Sorry if I'm being dense.

*NSCell has -setControlSize:.*

----
I've just got this working and since it was a long way from obvious I thought it was worth posting. The suggestion above to use setControlSize did not work. This is what I did:
    
//
[menuButton setFont:[NSFont systemFontOfSize:[NSFont systemFontSizeForControlSize:NSMiniControlSize]]];
//

-JasonSwain

----
That code alone will not work without calling     setControlSize:. All it does is change the font. If you're claiming that this alone is sufficient to make a small NSPopUpButton (which is what I understand from what you're saying), I would love to see a complete example so I can verify that it works.
----
I have checked my code and there is no call to     setControlSize:. I tried this first and it had no effect. Then I tried the solution described above and it worked fine. 
----
What's your operating system version?  I just tried it on Tiger, and     setControlSize: has an effect and is necessary.      setFont: changes the text size,     setControlSize: changes the bezel size.  You have to do both to duplicate IB's small popup.
----
The popup's cell takes the     setControlSize:.
----
Note for folks who have NSPopUpButton pull-down menus: *never* remove the menu item at index 0 on the menu.  You will get very, very weird results if you do this, and probably a lot of head-scratching to go with it.  (This is mentioned in the documentation, but only in a single sentence.)  -- AndrePang

----
Hi, does anyone knows how to populate an NSPopupButton with NSAttributedStrings using cocoa bindings. It seems like an impossible task using default bindings (contentValues only binds to an array of strings, but not attributed ones). The idea behind this is being able to display pictures along the text entries in the popup menu. I guess I could just use the old-fashioned controller approach but I wanted to implement this using bindings.

My guess is that I need to subclass NSPopupButton to add a new binding. Any ideas ? -- PierreChatel

----
If you get to the point of having to post here and consider writing a custom subclass just to accomplish this very simple task, it's probably time to ditch your bindings and write a little bit of glue code. It won't hurt.
----
yeah, I guess that's what I'll have to do... I'm just amazed at how bindings failed to let me accomplish this very simple task. The first one I ever tried with bindings in fact. Guess I'll just have to rollback to writing some glue code. -- PierreChatel

----

I've come to the conclusion that bindings are overrated, in the sense that they only really accomplish a fairly small set of simple tasks. Anything beyond that you need to do it the "old way" - write some code. And in that case maintaining code that has a mixture of both is more difficult in the long run, as you can't tell easily at a glance what operates using bindings and what is glued. Thus I believe that if there's any chance that you'll ever need to use glue, you may as well do it all that way for maintainability. And since you can't always tell when you'll need to do that, I no longer bother with bindings at all. Writing much glue code is tedious, but it only takes a few seconds - seconds you'll gain back tenfold six months down the track when you try and understand the code you wrote earlier. Bindings were flavour of the month when 10.3 came out, and everyone rushed to adopt it as the latest Apple "must have" technology - but are they really any good? I've come to the conclusion that  no, they're not. Be interested to know what other developers think now that the technology has settled in and their limitations are now becoming apparent. --GrahamCox.

----
I've basically come to the same decision. Bindings are convenient but they are extremely fragile. As soon as something goes wrong or you need something they can't do, the whole thing falls down. The basic impossibility of debugging them and difficulty of doing complex tasks make them basically useless in my eyes. -- MikeAsh

----
I think we, as a community, tend to try to think of the newest and greatest tech from Apple (bindings, CoreAnimation, maybe ObjC2, etc) as being a silver bullet, and of course there's no such thing as silver bullets. Bindings have their place, and it's sometimes a disappointingly trivial place, but they're still a useful tech when used well.

Myself, I'm happier with them than without-- but IB's interface to them is so painfully limiting... -- RobRix

----
I've come to the conclusion that I would never go back to the world without bindings. I think that people wanting to write tons of glue code are just a bunch of old farts.  I came along with Tiger, so I know nothing of 10.3 "flavour of the month". I'd never write an app without bindings, that I know for sure.

----
As one of those "old farts", I obviously must disagree. Bindings only make easy things easier. They don't do anything to make hard things easier, and often make hard things vastly harder. The minor bits of glue code they save hardly makes up for the extreme difficulty of debugging them when they inevitably break. -- MikeAsh

----
Thus far, every time I sit down with bindings, there is a lot of things I'd like to do that should be simple with glue code (a little tedious if you make a typo) but seem to be hard with bindings. Bindings just don't seem to offer as much as promised
-JJ

----
This little tutorial shows you how to use fixed values in NSPopUpButtons with bindings. You know, an "m" = "male" and "f" = "female" kind of thing: http://blog.springenwerk.com/2009/09/nspopupbutton-with-fixed-values-and.html

- Johannes

