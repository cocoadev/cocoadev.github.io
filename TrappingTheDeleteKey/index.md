---
layout: page
title: TrappingTheDeleteKey
---

In my subclass of General/NSTableView I wrote this:

    
-(void)keyDown:(General/NSEvent *)theEvent
{
	General/NSLog(@"General/MyTableView: keyDown: %c", General/theEvent characters] characterAtIndex:0]);
	
        unichar key = [[theEvent charactersIgnoringModifiers] characterAtIndex:0];
	
	if (key == [[NSDeleteCharacter || key == General/NSBackspaceCharacter)
	{
                General/NSLog(@"General/NSDeleteCharacter or General/NSBackspaceCharacter pressed");

		General/[[NSNotificationCenter defaultCenter] postNotificationName:@"General/DeleteNote" object:self];

                return;
        }
    
    	[super keyDown:theEvent];
}



So it works fine with backspace, but not with delete key (just beeps), and here's the log:

When I press backspace:

    
2004-12-03 02:36:59.481 myNotes[4964] General/MyTableView: keyDown: 
2004-12-03 02:36:59.481 myNotes[4964] General/NSDeleteCharacter or General/NSBackspaceCharacter pressed


When I press delete:
    
2004-12-03 02:36:58.201 myNotes[4964] General/MyTableView: keyDown: (


Why "(" ? What's wrong with my code?

----

Maybe it's General/NSDeleteCharFunctionKey? You're getting a "(" because you're using %c to log a unichar, and %c only deals with ASCII chars. Try %C instead, or better yet, %x to see the hex representation of the unichar value.

----

The problem is that General/NSDeleteCharacter doesn't pass the     if call. I tried General/NSDeleteCharFunctionKey, but it doen't work either. With     %x I get

for delete key:          2004-12-03 03:48:23.645 myNotes[5065] General/MyTableView: keyDown: f728

for backspace key:          2004-12-03 03:48:25.207 myNotes[5065] General/MyTableView: keyDown: 7f

 If I change the code from:
    
if (key == General/NSDeleteCharacter || key == General/NSBackspaceCharacter)

to:

if (key == General/NSDeleteFunctionKey || key == General/NSBackspaceCharacter)

Then delete key works, but backspace doesn't!

----

How about the following?

    
if (key == General/NSDeleteCharacter || key == General/NSDeleteFunctionKey)


----

You could also (when overriding -keyDown:) check to see if the first character is equal to 127 (the ascii code for the delete key). -- General/KevinPerry

You might want to look at using the deleteForward: and deleteBackward: methods instead, might be easier.

Easier maybe, but it seems sort of hackish, or inappropriate, unless he is actually trying to delete something and is not using that key for other  purposes. The documentation specifically mentions deletion (obviously) but does not mention the use of it to respond to events. Also, if there are other keys he wishes to intercept besides delete, you don't want some event handling code in -keyDown: and some in -deleteBackward:. Similar code like that should be consolidated in one overridden method. -- General/KevinPerry

----

I also believe that the -deleteForward: and -deleteBackward: methods don't get called unless you override -keyDown: anyway and call -interpretKeyEvents:, which limits its usefulness in this situation. -- Bo

----

Do I have to subclass whatever view I'm working with, and make the nib use that, just so that I can override General/NSResponder's keyDown: method!?  I'm looking at the documentation and there doesn't seem to be any delegate method that would seem appropriate... but that just doesn't seem right!  

And actually, now that I've subclassed General/NSTableView and implemented -keyDown: it never gets called... neither does init... it's still a General/NSTableView, even though the nib is telling me otherwise.

----

I had to subclass General/NSOutlineView and General/NSTableView... and I'm not really happy about doing it either. :(

----

I've seen the following done (in the Cocoa ports of the General/NeHe General/OpenGL tutorials, I think):  Make a custom subclass of General/NSResponder and override -keyDown: (and -becomeFirstResponder, -resignFirstResponder, -acceptsFirstResponder all returning YES).  Then from your table controller (or wherever) tell the General/NSTableView's window to -makeFirstResponder: with an instance of your General/NSResponder subclass.  Also, if you don't use a key, be nice and pass it down the General/ResponderChain with General/self nextResponder] keyDown:event]. I think this works rather well. It's worth a try, at least.  -- [[BrianMoore

----

One very useful General/NSResponder subclass that's already in the responder chain is your window controller.  You can add your -keyDown: method to that class if you wish to avoid subclassing a view class.  However, it'll be a bit more complex since you'll have to filter out key presses passed on from different responders (as in     if (General/self window] firstResponder] == myTableView)), and it won't let you override the first responder's actions, just respond to keys it passes up the chain.   -- Bo

----
What you want is something like this:

**[[NSTableViewWithDelete.h**:
    
#import <Cocoa/Cocoa.h>

@protocol General/DeleteKeyDelegate
- ( void ) deleteKeyPressed: ( General/NSTableView * ) view onRow: ( int ) rowIndex;
@end

@interface General/NSTableView ( General/DeleteKey )
@end


**General/NSTableViewWithDelete.m**:
    
#import "General/NSTableViewWithDelete.h"

@implementation General/NSTableView ( General/DeleteKey )
- ( void ) keyDown: ( General/NSEvent * ) event {
	id obj = [self delegate];
	unichar firstChar = General/event characters] characterAtIndex: 0];
	
	// if the user pressed delete and the delegate supports deleteKeyPressed
	if ( ( firstChar == [[NSDeleteFunctionKey ||
		   firstChar == General/NSDeleteCharFunctionKey ||
		   firstChar == General/NSDeleteCharacter) &&
		 [obj respondsToSelector: @selector( deleteKeyPressed:onRow: )] ) {
		id < General/DeleteKeyDelegate > delegate = ( id < General/DeleteKeyDelegate > ) obj;
				
		[delegate deleteKeyPressed: self onRow: [self selectedRow]];
	}
}
@end


WARNING: you should always consider the case where [events characters] is an empty string or you should get an "Index Out Of Bounds Exception". It appends for some dead key (not on all keymaps but it is possible).

----

http://developer.apple.com/documentation/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSEvent.html#//apple_ref/c/econst/General/NSDeleteFunctionKey

http://developer.apple.com/documentation/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSText.html#//apple_ref/c/econst/General/NSDeleteCharacter

If you want to be maximally portable, you should probably include *all* the relevant codes, as it may vary between computers :(

----
The specification of Categories specifically recommends against implementations such as the last one:
"Although the language currently allows you to use a category to override methods the class inherits, or even methods declared in the class interface, you are strongly discouraged from using this functionality. A category is not a substitute for a subclass. There are several significant shortcomings:"
http://developer.apple.com/mac/library/documentation/cocoa/conceptual/General/ObjectiveC/Articles/ocCategories.html#//apple_ref/doc/uid/TP30001163-CH20-SW1

In the case of a table that is bound to General/CoreData via an General/NSArrayController, this tables keyDown: method is clearly being handled by another category because the up and down arrow keys work on the table.  As soon as this (General/DeleteKey) category is installed in General/NSTableViews those up/down/enter keys stop working and there is no recommended way of passing the keys on to the category that was recognizing the up and down arrow keys, and the enter.
  
Unfortunately this makes it so that what would be a nice clean implementation of a (General/DeleteKey) category, now has to handle all possible keys passed to the General/TableView.  It would be far better to install an General/NSResponder as stated above because it can handle just dealing with the delete key(s) while allowing the other responders in the chain to handle the other keys.
