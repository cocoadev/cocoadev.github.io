---
layout: page
title: SettingContentOfAProgrammaticallyMadeUI
---



I have a listView NSView in my window. The purpose of this view is to hold a list of elementView NSViews.
The UI of elementView has been made in IB, using an instanciated view populated with UI elements (NSTextView and NSButtons...).

    
- (void)addItemView
{
	//-- Reading nib :
	if ([NSBundle loadNibNamed:@"MyUI" owner:self])
		NSLog(@"nib loaded");
	else NSLog(@"Failed to load Content.nib");
	float elementHeight = [questionView frame].size.height;
	//-- Buiding UI
	MyItemView *itemView = [[MyItemView alloc] initWithFrame:NSMakeRect(0, yPos, 400,200];
       yPos += 200;
       [myListView addSubview: itemView];
	
	[itemView release];
}


This code is working as expected but i'm getting in trouble when i'm tryin to access the 'content' of my UI.

How can i change or retrieve the text of the NSTextView of the itemView? I mean, i'll have several instances of itemView and each instance will load its UI from the nib file, so how outlets are working in such case? I tried several way : if i connect the outlet from my controller, i can change the text but it always change the text of the last instanciated itemView... If i try to connect the outlet from the NSView subclass itemView itself, it don't works at all..

----

You will need to keep an array, mutable probably, of your items as the class. You then access the one you want by getting a reference to it from your array and changing things as you want.  Just adding them to the ListView won't let you get access to them. You need to have access to the item in code to change things.

----

In fact i have already done that, i just tried to minimize the source to focus on my problem : connecting UI on the fly.

In my current project, i have a controller, a model class (NSObject) storing my parameters, and a view class (NSView) supposed to show the model parameters on screen through NSTextViews and NSButtons states.

My problem is mainly an outlet problem : when my controllers is informed to update a value, it changes it in the model but don't know how to tell the view to update itself according to the model. That's because i don't know how to connect thing programmatically i think.

I tried this :

* I added     IBOutlet NSTextView *myText in the .h of my NSView subclass : MyItemView

* I instanciated a view in IB and set its class to MyItemView

* I opened the view, dragged a NSTextView on it and connected the outlet myText from my view instance to it.


After this, i added a method to MyItemView :     setText:(NSString *)aText

When the model change, the controller send a     [aMyItemViewInstance setText:newStringValue] to one of my MyItemView instances in the list (i retrieve the view using [mainView subviews]).
The method is called as expected, but the NSTextView is not updated. It looks like the outlet is not correct.

I almost sure it's not the good way to do this, but there is so few example about programmatically made UI...


----

I think  what you need is to have a separate nib for your elementView, where the file owner is an instance of your controller for the elementView. Then you can instantiate the nib and get an instance of your controller all connected the view. Then you get the view from the controller and add it to your superview. At some point, you need to connect the model and the controller too. You will instantiate that nib as many times as you need for each subview. How about that?

----

You're right and that's exactly what i did. But when i instantiate that nib as many times as i need for each subviews, i have this problem with my outlet :     IBOutlet NSTextView *myText;  is connected but -as i said above- if i ask the controller to do     [aMyItemViewInstance setText:newStringValue], it will always change the text value of the last itemView. If i instanciate 3 itewViews, how can i change the text of theNSTextView of instance 1 or 2?

I'm think i understood how the MVC is working but i'm not yet used to make it works in cocoa. Maybe something is not correct in the way i'm doing this?
Maybe it's better not to use IB at all and build the UI all programmatically? But what does it change in connections? Do i have to make collections of connections?

According to what i read on the net, cocoa is not really good for programmatically made interface... But i also read that all the work you do in IB can be done programmatically so their no reason i can't do it, phew!

So, is anyone used with programmatically made UIs?

----
What you are doing looks like the right way. I reread (again) your original description. Sorry, it was indeed what you are doing. And it looks like you have everything right.
To help with the debugging, maybe you should first check wether the outlet is right, or if something is wrong with     setText:. I suppose your     setText: is quite simple, but you did not show the code ;-). Anyway, you could simply check     [oneView myText] contents and see if that is right. Even better, set a breakpoint somewhere in your controller, and explore the different values of the views, text views,... to check if ewverythinig is right.

As an aside, i believe text editing uses a shared instance of one of the text class (can't rememeber right now), so there could be something messy going on. When the user edits something, the text view uses that shared instance, and this shared instance is later being reused by the various text fields whenever editing occurs.

