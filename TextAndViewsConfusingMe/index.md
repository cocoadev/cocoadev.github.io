---
layout: page
title: TextAndViewsConfusingMe
---

Describe General/TextAndViewsConfusingMe here.

Hey

I am very new Cocoa and I am having trouble doing some very basic things (what I think are basic). I have a General/NSWindow, which contains multiple views. On each View I want a label, or some form of text field. By Label I mean a text field that is not editable, not selectable, not anything, just TEXT. I also want to have the possibility that instead of a label there could be an image. I have therefore now got two problems. So far I have inherited everything from General/NSView in a customer class and I have a union containing General/NSTextField, and General/NSImage. My current and biggest issue is first setting up the label and displaying something on the View.

I have looked everywhere for documentation, but everything iss with using the interface builder. I want to do this only with code. I was hoping it would be as simple as:

General/NSTextField *a = General/[[NSTextField alloc] initWithString:@"What I am"];
[aView addComponent:a];

Or something along those lines. I have found nothing to indicate that it is this easy. Can someone explain or point me in the correct guide direction.

Is there an easier way to let the user choose to use text or an image (from a coding standpoint). PS: I am trying to use General/NSTextField so that the text could be changed from the Font menu.


Thanks for any help


Edit:
I have found the following:
<code>
	    General/NSTextStorage *textStorage = General/[[NSTextStorage alloc] initWithString:@"String"];

	General/NSLayoutManager *layoutManager = General/[[NSLayoutManager alloc] init];

	General/NSTextContainer *textContainer = General/[[NSTextContainer alloc] init];

	[layoutManager addTextContainer:textContainer];

	[textContainer release];

	[textStorage addLayoutManager:layoutManager];

	[layoutManager release];

	General/NSRange glyphRange = [layoutManager glyphRangeForTextContainer:textContainer];

	[self lockFocus];

	[layoutManager drawGlyphsForGlyphRange: glyphRange atPoint: rect.origin];
	[self unlockFocus];
</code>


But that seems like a lot of code. A lot of duplicate code also if I need to create a seperate General/NSTextStorage, Layout Manager, etc, for each view I want to display text in.

Is there an easier way?


Edit2: I should also note I am trying to make this efficient as I might have to draw up to 100 pieces of text on 100 different views. They will however all be short pieces of text.

----
1) You are completely on the wrong track.  The code you included is not appropriate for the task you describe.
The General/NSCell class can already display images or text or both using attributed strings.
There is an example in Apple's documentation regarding manually setting up a full General/NSTextView based editing system in a scroll view.  You will prefer using Interface Builder.
Here is an example that programatically creates views and adds them as subviews to other views.  http://www.cocoabuilder.com/archive/message/cocoa/2004/10/7/119005 The example used General/NSMatrix, but that is just a kind of General/NSView, and the technique is general.

2) Why don't you want to use Interface Builder?  It will save you from writing a lot of tedious and error prone code.

3) You need to work through at least one introductory Cocoa tutorial.

4) There are several examples of writing Cocoa code without Interface Builder. http://www.cocoabuilder.com/search/archive/cocoa?words=without+interface+builder
Chapter 4 of the Garfinkel & Mahoney book from O'Reilly is rumored to be an extended example and explanation.



5) Everything you need to know is documented at http://developer.apple.com/cocoa/

----
1) Thanks, I will look into General/NSCell. Unfortunately, on first glance it looks to primitive. I need to be able to rotate the images and text. I might look at putting the cell into the view. This way I can rotate the view. I agree though that is I was doing some basic operations this would be the way to go. Also, I need to be able to draw things directly into my view using Quartz. I still need to do more research though, especially when used in combination with General/NSMatrix. Thanks for the help :)

Also, I have done a few tutorials from the developer.apple.com. Most used the interface builder however which I am trying to avoid if at all possible just to see how things work at the code level.
