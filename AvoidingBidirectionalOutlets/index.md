---
layout: page
title: AvoidingBidirectionalOutlets
---


Hi all, I've been struggling with this for a while, and it seems there's some basic misunderstanding on my part. Here's what I'm trying to do:

I have a custom subclass of NSMatrix that I want to display some thumbnails. It should be possible to drag'n'drop files to this view and have them appear as thumbs. i have a controller to handle this, and massage + store the new data in Core Data. This controller is instantiated in IB, as is the custom NSMatrix. My question is, how do I connect them?

I need the view to be able to tell the controller that files were dragged, and the controller to be able to tell the view to update with new data. Trouble is, having an IBOutlet in each class and connecting them bi-directionally brings all sorts of headaches. It only works if at least one of them isn't typed, so it reads "IBOutlet id" instead of "IBoutlet QCMController". This gives a "QCMController may not respond to [whatever method I'm calling]" warning, but it works.

I've looked for a way to programmatically trigger an IBAction, but can't find it. In this setup, my concludeDraggingOperation method in the custom view would trigger an IBAction in the controller, but I can't figure out how.

What's the right way of doing this?

----

I'm kind of confused, but I think you're going about it the wrong way. IBActions are just a way for InterfaceBuilder to know which methods in your controller are actions for a control. You call them in code like you do any other method. For example:
    
- (IBAction)clickedButton:(id)sender;

Can be called like:
    
[self clickedButton:nil];


----

There should be no problem using a typed outlet in both your custom view and its associated controller.  Why can't you use proper types for both?

----

Also, the best way to send an action to a target is to use the NSApplication instance method -sendAction:to:from: as in the following snippet of code.  (Note that NSApp is the global NSApplication instance.)

    
if ([NSApp sendAction:[self action] to:[self target] from:self]) {
    // action sent successfully
} else {
    // action not sent successfully
}


The reason to use this rather than invoking the action yourself is that it will properly handle the case of sending an action to the nil target, which actually sends the action through the responder chain.

----

Thanks for your replies, they set me on the right track.

I was trying to #import the respective headers in each other, which threw the compiler. And having been assured that bi-directional connections are OK, I found out about the @class directive. It works like a charm now.

What should I do with this page?

----

See WhenDiscussionsConclude

----
Allright, since this is really a dumb newb mistake, I'll orphan the page. Thanks again!

