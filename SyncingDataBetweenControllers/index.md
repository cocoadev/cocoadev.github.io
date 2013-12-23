---
layout: page
title: SyncingDataBetweenControllers
---

This is more of a logic question Im building a new version of my app that relies a good amount on cocoa bindings to help make things easier. I started out with my apps main controller (General/NSObject subclass) which stores 2 General/NSMutableArrays which other General/NSWindowControllers will need to keep in sync with when changes are made in those controllers and vice versa with the main controller if those controllers are launched in the app. I didn't know if this would work, but I tried just dragging and instaniating my main controller class in another nib which a General/NSWindowController loads if it's started in my app so I could use Bindings to fill a tableview with data. Doing this caused the main controller to do it's init again (as I have a log utility and saw it spitting out the messages it puts out on init) so I thought id move this code to awakeFromNib and it still does this again.

Apparently though Cocoa bindings works perfectly fine from the other nib as far as I can tell at the moment, still I can't tell why it would call the main controllers awakeFromNib method again as this has me worried if I go through this way it would probably cause big bugs. My question here is if you are using Cocoa Bindings what is the easiest way to make sure General/NSMutableArrays are synced in various General/NSWindowControllers (my app only has about 3 right now) for use with Cocoa Bindings in my app? I was hoping Cocoa Bindings could make this easy but it has me worried when it's apparently going through my main controllers init and awakeFromNib methods again.

I don't know if this has been discussed before as I couldn't find anything with the search terms I tried.
