---
layout: page
title: ImplementingAGoogleSearchInMyBrowser
---

I'm a Cocoa and Obj-C newbie and I've been following the "BYOB: Build Your Own Browser" tutorial series over at MacDevCenter to try to learn some Obj-C and Cocoa. I have a basic browser built now, but I can't for the life of me figure out how to put in a functional Google search field. I done everything needed, such as add an outlet and connect it, define the outlets and actions in the .h file, and code the actual implementation in .m file. The code in the implementation is identical to the connectURL's implementation at the moment, because when I modify it, it causes errors. Here's the code I thought was logical to use:

    
//This is at the top of the .m file:
static NSString *googleURL =@"http://www.google.com/search?q=";


//This is at the of the .m file, right before @end:
- (IBAction)googleSearch:(id)sender{
    [googleField setStringValue:[sender stringValue]];
    webView mainFrame] loadRequest:
        [[[NSURLRequest requestWithURL:
            [NSURL URLWithString:
				googleSearch+[sender stringValue]]]];
}

But it doesn't work - It just gives a parsing error. What's wrong?

----

ObjectiveC doesn't support support concatenation using the + operator.  Try using the     -stringByAppendingString: method instead.  You'll have to do quite a bit more preprocessing on your query string in the long run (i.e. replacing spaces with + signs, %-encoding non-alphanumerics, etc.), but that should get you started.-- Bo

----

Also, is there a reason why you're doing     [googleField setStringValue:[sender stringValue]];. I would think that     sender == googleField.

