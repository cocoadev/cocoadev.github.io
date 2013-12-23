---
layout: page
title: ExtractParametersFromAppleEvents
---

I need to add custom 'odoc' handling to my application in order for it to support the ODBSuite.

I have managed to setup my handler for aevt/odoc, and I do get the event. I can also get to the AEDesc, and see that the code is 'aevt', and I can extract the data using AEGetDescData(). But I have no idea what to do with the data... 

I have read http://developer.apple.com/documentation/Carbon/Reference/Apple_Event_Manager/index.html but this doesn't contain any conceptual info, and all the functions (except the one mentioned above) works with types other than AEDesc.

I found this page: http://www.mactech.com/macintosh-c/chap10-1.html

It reveals that AppleEvent is a typedef for AERecord, which is a typedef for AEDescList, which is a typedef for AEDesc, which is a structure with the type ID and an opaque data handle � so I can use some of the other functions with my AEDesc, like AEGetParamDesc.

The function takes a keyword, but what is a keyword? If I specify keyDirectObject I get back an AppleEvent of type 'list' which I assume is the list of file aliases for my odoc event, but specifying kAEOpenDocuments as keyword makes the function fail.

And what's with both parameters and attributes? Are each parameter an argument to the "root" event, and attributes are sort of parameters to the parameters?

----

Apple event code is everywhere. Try searching ADC website (it does have a search function) for the function names or other relevant information you are interested in. odoc is very common.

----

The samples set up a Carbon AE handler and register for aevt/odoc -- I am using Cocoa, and although I can setup an AE handler for odoc, it won't really work (most of the time), because NSApplication eats the event.

So instead I query the current event in the application delegate methods, but I haven't found a way to ensure that the event is of type odoc, AEEventID() returns aevt, not odoc.

Furthermore, the samples I've found do something like this:
    
AEGetParamDesc(event, keyDirectObject, typeAEList, list);
AECountItems(list, &total);
for(SInt32 i = 1; i != total+1; ++i)
   AEGetNthPtr(list, i, typeFSS, &keyWord, &returnedType, &fileSpec, sizeof(fileSpec), &actualSize);


Now this might be fine for just opening files, but a) I still do not know what the keyword actually means, 2) is using typeFSS really that good an idea (i.e. isn't it too limited to support current paths?), I've tried other types, but all but typeWildCard will fail when events are sent from the ScriptEditor (and the actual type it sends is 'obj ', which I have no idea what is), and 3) as said I must support the ODBSuite, so I need to retrieve extra parameters/attributes/descriptors or whatever, and I have no idea about how I'd obtain these...

So thanks for the tip, but I am really after either sample code which does *exactly* what I need, or some decent documentation...

*Okay, the keyword is the parameter identifier -- not sure if keyDirectObject is a magic value (like keyMissedKeywordAttr) or if it's just the convention to use this for the primare argument.*

----

Okay, let me answer some of your questions:

Conceptual information about Apple Events can be found at http://developer.apple.com/documentation/mac/IAC/IAC-94.html.

An attribute is effectively a special kind of parameter. Attributes are either traits of the event itself, like whether a response is needed, or an environmental property, like the current "considering" status.

An 'obj ' is an object specifier, i.e. an element or property of an application-defined object.

keyDirectObject is just the unnamed (aka direct) parameter. The command "open x" would have a keyDirectObject of x. Usually, the direct object is the target of the command. See DirectParametersAsValues for more detail on that.

And, for bonus points, here's some advice:

You might want to use NSAppleEventDescriptor instead of AEGetNthPtr, AEGetParamDesc, AEGetDescData, etc. It has an -eventClass and an -eventID method, which should return 'aevt' and 'odoc', respectively, for an aevt/odoc event. To get elements of a 'list' parameter, use -descriptorForKeyword: to get the NSAppleEventDescriptor corresponding to the 'list' parameter, then -descriptorAtIndex: on to get an NSA.E.D. for each element, then -int32Value or whatever to get the actual value.

I don't know how reliably you are getting aevt/odoc events with your method, and I don't know what kind of custom processing you need, but if NSApplication's -application:openFiles: delegate doesn't work for you, I would suggest overriding -handleOpenScriptCommand:. That has the advantage of turning the event into an NSScriptCommand instance, which is easier to deal with then an NSA.E.D.

For general info on supporting AppleScript in Cocoa, check HowToSupportAppleScript.

�DustinVoss

