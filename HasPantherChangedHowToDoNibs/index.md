---
layout: page
title: HasPantherChangedHowToDoNibs
---

Has there been a change in Panther on how IB handles multiple nibs? I have 2 Cocoa books...Learning Cocoa with Objective-C and Building Cocoa Applications. In both of them, in their sections on multiple nibs, they talk about making a controller class and then assigning the new nib's File Owner to that class by clicking on the File Owner in the Instance Panel, and choosing the class in its Attributes window. 

I find though that if I click on File Owner in the new nib's Instances panel (or any nib's Instances panel), the Attributes option in its Info panel, it just says Not Applicable. Both these books were written for 10.2.8...has Panther changed this?

*You need to choose 'Custom Class' from the Inspector panel's popup menu. Then select the class in the list.*

----
Ah, thanks. One more question on a similar vein - I have 2 nibs now: a main document, and a panel that gets called up when the user selects an item in the main menu. When the user chooses the option that brings up the panel under the menu, they enter some data into a text field in the panel.

The panel is controlled by a class called Controller, which is instantiated in MainMenu.nib. This class, once it gets the data, does some parsing, error checking, etc. Then it needs to either send this data to the document and its network of subclasses, or get a data field from the document. The thing is, I'm not sure how to do this. Controller is instantiated in MainMenu.nib (this is document-based architecture), and MyDocument has its own nib. I don't see how I can get an outlet between Controller and MyDocument, since if I instantiated a MyDocument object in MainMenu.nib, wouldn't it be a different MyDocument that what the user is seeing? Similarly, wouldn't instantiating a Controller in the MyDocument.nib make a different Controller object than the one the data was given to?

----

If you want to invoke methods in your document class(es) from objects in the main menu nib file, you need to learn a bit more about the responder chain.

There are a couple of good pages here on the subject - just search for ResponderChain or MakingNibsTalkToEachOther

----
I read the two links, but I still have a bit of a gap in my understanding. I set it up so that the panel can trigger an action in MyDocument by creating an action in the FirstResponder in the panel's nib and MyDocument.nib. The thing is, I still don't understand how to send data gathered by the panel (an NSTextField) and worked with in the Controller class to MyDocument. FirstResponder can't do outlets, so how do the panel and MyDocument share data? Or am I missing something really obvious?

----

If you can trigger an action, you should be able to fire off any method (tell me if I'm wrong!)

I don't have a feeling whether it should be obvious or not, but when you can't simply create an outlet pointing to one of your ivars, you
have to write an accessor. Someone will be sure to correct me if I am guilty of poor style, or am downright wrong.
(You may not be able to put a method that is not an IBAction into your responder chain. I've never tested this - so I don't really know.)

In your case, the method in your document can take some argument(s)
to get to the data. This is clear in my brain, but I'm still not confident I can make it clear to you. If I can't, that won't be your fault! :-)

It seems to me that if you can access a method in MyDocument from your panel controller, if that method has an argument, you can
pass the data you need in that argument. But if you can only invoke an IBAction (which has an argument (id) sender, then
maybe you could use something like [ sender accessor ] to get the data.

Egad! Now I'm even confusing myself!  The situation is too abstract for me. Perhaps if you could share the relevant code....

----

Good idea. The project is small, so I posted it to my webspace, along with an RTF describing the problem in what I hope is a more organized fashion.

http://www.student.richmond.edu/~kh5di/Project3.zip

----

I'm shooting from the hip here, since I don't have time to dissect the entire project.

Is there some reason your panel controller is in the main menu nib file rather than in your document nib file?

Try putting the panel controller in your document nib.
Or you can instantiate a special window controller class for your document (recommended)
There is an example of this in BookBuildingCocoaApplications.

I mean, the document is entirely in charge of displaying the panel.
Not only that, the panel settings apply to a document and not to the application as a whole.
If the action to display the panel is in the document class, you can still get to it since it is in the responder chain
that the main menu has access to.

I think that is where your misunderstanding of the chain is. Quoting from MakingNibsTalkToEachOther:
*Another method would be to use the FirstResponder. To do this, first create the action you want to be called in the NSDocument subclass in MyDocument.nib. Then double click the FirstResponder in Properties.nib. Add the action with the same name to the FirstResponder. When calling the FirstResponder's action, it will go down the ResponderChain and call the first object that replies to that method. In other words, the currently active document. Note: be careful using common method names for this - you don't want another object to respond to the method before it gets to the document.*

Naturally, if the panel objects are in the same nib as the document objects, you should have an easier time connecting outlets and
accessing the data in them.

*It should also be brought up, that if the panel is in the MyDocument.nib file, a new panel will be created for every document. Is this the desired functionality? If not, then it will need to be in a separate nib file.*

So if you just add a method to the panel controller that accesses the data in the panel's text field and put it in the responder chain in FirstResponder, you should be able to use it in the document class, with the caveat mentioned above.

----

Ahhh, so I'm thinking of it the wrong way. I can link the main menu option to the FirstResponder object in MainMenu.nib, then have MyDocument carry the action method to open the Panel and create Controller in MyDocument when needed. I think I got it. Thanks a lot! Couldn't have done it without you.

----

I understand how you might want to access document actions from the main menu. It's a necessary thing, many times, and you probably just want to make sure you know how to do that. Looking at the layout of your app, I don't really see why you wouldn't squeeze the necessary control(s) onto the main document window (except that it's getting very cluttered there!)

----

(Congratulations on decyphering NSResponder!, BTW. That's the right way to go. Very appropriate.)

