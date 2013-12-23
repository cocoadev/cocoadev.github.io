---
layout: page
title: DashboardGadgets
---

People have started posting these on the web (what NDA?)...

Fill in the blanks about Gadgets and Gadget Plug-in development here :)

----

I don't think it is covered under the NDA as there was a competition at WWDC to produce them.

*But participants of WWDC did sign an NDA!* Did the NDA cover the gadgets? *i'd imagine so. it wouldn't be a very good NDA if it didn't.*

As for developing them, they work perfectly in Safari (except for the animations). You can call Cocoa if need be but I have not seen the API in that regard. -- MatPeterson

----

I am not a WWDC attendee... this is taken from the examples found online
here's some gadget API (all methods/properties I found on the gadget object from the examples)

    
// control circles and rects define areas where buttons appear
// to create them you must first openControlShape... 
// then addControlShape for each one then closeControlShape..
openControlCircles();  
addControlCircle(x,y,r); //x and y may be reversed?
closeControlCircles();
openControlRects();
clearAllControlRects();
addControlRect(x,y,w,h);
closeControlRects();

// not sure of the purpose of these ones
// maybe for controlling the flip state?
startEdit();
endEdit();
runEditTransition();

//these should be pretty self explanatory...
resizeTo(w,h);
openBundleID( bundleString ) //eg "com.apple.iTunes"
setPreferenceForKey(pref,key) // using user defaults ?
preferenceForKey(key)
onkeyfocus // the function called in response to key focus
onkeyrelease // see above
onhidden
onremove
onselectall
onshown
identifier // probably the name of the gadget


it appears there is a plugin system for adding the extra functionality to the environment... probably you write code that publishes an api, put it in a bundle somewhere in your Library and it get's picked up when you start the environment.  This is gleaned from the Calendar example, search for "Calendar" (case sensitive) and you'll see a comment that //plugin not ready yet.... also the addressbook example uses an object called AddressBook to query the address book API but I can't see anywhere in that code where it is imported, unlike the iTunes example... comparing with the copy of iTunes I have, it doesn't appear that the API for iTunes is based on applescript, although it may be so...

there are some special styles that can be assigned to elements in the html too... like "-khtml-something"... they all start with -khtml- so they are probably already present in webkit and konqueror
----

Installing XCode 2.0 gives you some Dashboard examples, as well as some little documentation and a little "tutorial" (and we all know what Apple tutorials are like).
Anyway, writing gadgets are very much done through HTML, CSS and Javascript. You can make pretty much using this method. To do more, like calling other applications or doing more advanced stuff etc, you will have to make a gadgetplugin (as seen in /System/Library/GadgetPlugins). They are based on the WebScript-stuff from WebKit and are very easy to implement. Then you just add the plugin to the gadgets info.plist and you got it inside your javascript code. It's pretty great, acctually.
Would be even greater, though, if there were any way to make it all in cocoa. Using NSViews.
But still, this is really really cool. --AntonKiland

----

That's pretty cool actually... so basically a GadgetPlugin is a regular bundle with it's principle class conforming to an informal protocol like:
    
//NOT THE REAL THING! Is it like this?
@interface NSObject (GadgetPlugin)
-(NSString*)gadgetPluginIdentifier; // maybe this is actually in the bundles info.plist
-handleJavascriptFunction:(NSString*)theFunction withArguments:(id)theFirst, ... ;
@end


 I really like the JavaScript language, but always wanted to add custom classes and stuff to the environment.  DashBoard looks almost exactly like the JavaScript environment I always wanted.  (sorry Konfabulator... you use custom xml layouts, not html and css... your JavaScript engine can't be extended to my knowledge.... you sit UNDER my apps, not coming over usefully and filling up the screen... and you ask me to pay money for a runtime that I have make things for)  By the way... does anyone know what the level of LiveConnect support is at in WebKit these days... 

----

Contrary to what I had written before as my thoughts as a non-WWDC attendee on gadget plugins as bundles... DaveHyatt mentions them in connection with Mozilla XPCOM plugins... I looked through the docs for creating those, and it doesn't look as simple as it could be for adding scripting support... anyone care to elaborate? (Correction: uses the mozilla NPAPI... check out the updated parts)

----

want to rant... maybe you're looking for RipOffRants... *This is getting old...**

----

They're called ***widgets***, not gadgets! Sheesh! *they were called gadgets when this page was created.*

