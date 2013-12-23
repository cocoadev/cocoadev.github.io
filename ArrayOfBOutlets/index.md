---
layout: page
title: ArrayOfBOutlets
---

I was wondering if it is possible to create an array of IBOutlets such that Interface Builder will let me select these as outlets.

Currently, I've tried this, I declare

    
IBOutlet id oneOutlet;
IBOutlet id arrayOfOutlets[20];


within my test.h file.  When I drop test.h into IB, it parses it without a problem and lets me select oneOutlet as a valid outlet, but I cannot select arrayOfOutlets[0], arrayOfOutlets[1], etc...

Is this possible.  I might need to look into NSMatrix if I can't do this...

Thanks..

----

There's no difference between IBOutlets and any other instance variable. IBOutlet is just a key for IB to pick up on when you drag a header file into it. If the outlets are objects, just add them to a NSArray, but you still won't be able to do anything with it in IB.

    
NSArray *arrayOfOutlets = [NSArray arrayWithObjects: oneOutlet, ... , nil];


----

NSMatrix sounds like what you're looking for. A little more detail about what you're trying to do might be helpful.

*NSMatrix doesn't sound like what you're looking for to me... have you seen FoundationCollectionsPalette? With it, you could have an IBOutlet connected to an NSArray instance inside your nib which you would populate with whatever you wanted to connect to.*

----
The details of the app are this:  I have about 20 NSSliders all connected to a single action.  Each slider has its own unique tag value.  I also have 20 corresponding NSTextField's that get updated when I move any of the sliders.  My CONCEPTUAL way of acheiving this is by creating an array of IBOutlet NSTextField's and when , say slider with tag '7' is moved, outletTextField[7] will be updated.  So within IB, I need to make(connect) all 20 NSTextField as outlets.  My naive method of creating an array of IBOutlets just doesn't show up within IB.

This might lead to an alternate question then, if I create 20 NSTextFields within IB, can I access them without connection them as IBOutlets?  Perhaps I can interrogate the NIB and assign the outlets programmatically?

----

You don't need to do anything with outlets for this - just connect your sliders' target to the takeDoubleValueFrom: action of their corresponding text fields, and vice-versa if you want to allow text entry into the fields. You could also use the takeFloatValueFrom: or takeIntValueFrom: actions, depending  on what precision you need.

----

I gave this a shot but I hit a snag.  This is what I for do within IB: I ctrl-click FROM my slider to my appController.  This is so I can do my magic with the slider values.  But now that I've done this, if I attempt to ctrl-click on the slider again and connect to the NSTextField, I don't see the option to takeIntValueFrom.  If I disconnect my slider from my appController and try connecting to the NSTextField, it does work.  So how can I connect them both.  I must be missing something easy here...

*No, you're not, you can't connect multiple actions, unfortunately. You could probably do this with CocoaBindings if you didn't mind learning to use them and didn't mind your app being Panther-only. I still think FoundationCollectionsPalette may be your best bet, though.*

