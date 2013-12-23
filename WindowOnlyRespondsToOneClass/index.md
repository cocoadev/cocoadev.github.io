---
layout: page
title: WindowOnlyRespondsToOneClass
---


I would appreciate any help with the following conundrum-

I have the following classes:

General/CustomView
CustomWindow1
CustomWindow2
General/DealWithData

here's the scenario:

General/DealWithData is the primary class and contains methods for setting the alpha of CustomWindow2

CustomWindow1 is an General/NSWindow that is connected to General/DealWithData by an General/IBOutlet - it is the original window from the original NIB created when I began this cocoa app (I suspect this is significant)

CustomWindow1 contains General/CustomView, which is registered for drags
General/CustomView can reference the methods in General/DealWithData through a singleton instance of General/DealWithData

CustomWindow2 is an General/NSWindow that is also connected to General/DealWithData by its own General/IBOutlet
CustomWindow2 contains some textviews and other data



What I want is for the calls in General/DealWithData that control window opacity of CustomWindow2 to be activated by a drag event in General/CustomView (which is contained in CustomWindow1).  
So what I am doing is as follows: from within General/CustomView, I get the singleton instance of General/DealWithData, and when I get drag events I call [instanceOfDealWithData changeWindowOpacityOfWIndowTwo]

strangely, the events in General/DealWIthData are in fact called (because I have General/NSLogs that get triggered), but the opacity of CustomWindow2 is unchanged.  If I create buttons in CustomWindow1 with actions to call those very same functions that were ostensibly called by the drag event, then the opacity changes!  

So then I try adding the opacity controlling methods from General/DealWithData to General/MyCustomView (which lives in CustomWindow1) and try to directly reference CustomWindow2 through an General/IBOutlet, to avoid General/DealWithData altogether.  Everything links up fine in General/InterfaceBuilder - but I am simply unable to affect CustomWindow2!  it seems to only respond to commands originating in General/DealWithData...any idea why this would be or what I could look for?  is there some property of General/NSView that makes it unable to control another window, however indirectly it does so?
----

sounds like this problem:

General/NSViewRedrawsOnObjCMethod

----

nope, it was a problem with my implementation of the Singleton.  First time singleton users: read the entire thread before using it to make sure you understand what is going on.  Don't forget to override -(init) to establish a static instance right off the bat!
