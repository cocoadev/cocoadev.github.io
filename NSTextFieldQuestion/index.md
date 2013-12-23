---
layout: page
title: NSTextFieldQuestion
---



I have NSTextField on my UI, and I want to change its string from inside of my program.  What is the function call I should use?  Or should I use NSTextField at all?  Please help.

----

    
[textField setStringValue:@"RTFM"];

//If you have any trouble sounding condescending, find a Cocoa user to show you how it's done. :-)

Boy, you can really spot a Obj-C/Cocoa noobie when they call it a function instead of a method. :)

----

Okay. Enough with the canonical NSTextFieldQuestion. Let's put this page to work. I'm not such a newbie, but I am putting this question on a page where I can expect to get, as the Rolling Stones would put it, my fair share of abuse. There is just a hole in my understanding of the basics I guess. Maybe the question would be better off on the NSP**'asteboardQuestion page, but I didn't want to create one just for this frick-frack.  ;-)

When a text field is selected, you generally can invoke the copy command from the Edit menu, and its contents will go on the pasteboard. I want to know how to perform the same operation programmatically, e.g. from within a method that is triggered by a callback from the Services menu of another application. I have tried

    [ pboard setString: [ outputField stringValue ] forType: NSStringPboardType ]

but it seems somebody else owns that pboard at the time I send the message; I've just started to fool around with advertising my app as a service, so I'm a newbie in that department as well. The pboard that is the receiver of the above message is passed in as an argument to the method where the message is sent, i.e., my Services callback. Everything else I've asked the method to do has gone without a hitch.

I make sure the text field selects itself before trying to copy from it. I was thinking if I could just it send the message that performs the Edit menu's Copy command, but NSTextField does not, of course, receive that message. My nib is basically out of the box, and the Edit menu Copy item sure enough sends the copy: message into the responder chain. But because the app is not in front (it is being used as a service, and I put some code in to keep it hidden if it is not in front, and hope I can keep it that way) the responder chain is going to be a bit useless to me. So I guess I need to get myself a new pasteboard. Je ne sais pas.

*Solved! The method that actually changes the contents of the text field is one that is being invoked when its implementing object receives a particular notification. When I created a general pasteboard and set it up in that method with its     declareTypes: owner: message, the     setString: forType:  message worked fine for me. Hurrah!*

