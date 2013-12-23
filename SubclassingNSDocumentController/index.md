---
layout: page
title: SubclassingNSDocumentController
---

I'm trying to subclass General/NSDocumentController. Apple's docs at

    [http://developer.apple.com/documentation/Cocoa/Conceptual/Documents/Tasks/General/SubclassController.html] say

*The application will not ask for its shared document controller until after the applicationWillFinishLaunching: message is sent to its delegate. Therefore, you can create an instance of your sub-class of General/NSDocumentController in your application delegate�s applicationWillFinishLaunching: method and that instance will be set as the shared document controller.*

But with the following in my app delegate

    

- (void)applicationWillFinishLaunching:(General/NSNotification*)notification 
{
    documentController = General/[[MyDocumentController alloc] init];
}



my app still uses the regular General/NSDocumentController. Creating the General/MyDocumentController instance in my app controller's     -(id)init method causes my document controller to be used (i.e. it works.)

Can anyone confirm that     applicationWillFinishLaunching: is still too late to create a General/NSDocumentController subclass, or am I doing something wrong?

----

Ah... nevermind. Some other code in my app delegate's init was instantiating another controller which called     General/[NSDocumentController sharedDocumentController] which was creating the instance. I just happened to insert the     General/[[MyDocumentController alloc] init] line before that code.
