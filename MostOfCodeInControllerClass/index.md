---
layout: page
title: MostOfCodeInControllerClass
---

I've been writing a program and I have like 600 hundred lines of code in my Controller class. That is pretty much my only class in the whole program. I only have one more class for a TableView item and that class only has about 100 lines of code. Is it a bad thing that most of my code is in my Controller class, or do you some times need to do that? If it is a bad thing, what advice can you give me to keep myself from putting all of the code in the Controller class?

----

The Cocoa paradigm is ModelViewController. Since View comes with Cocoa (and hence requires no code), one need only consider which takes more code: the Model or the Controller? It is quite possible for it to be the latter.

If your Model classes, however, are excessively simple, you might find yourself repeating the same chunks of code several times in the Controller. If this is the case, try refactoring these common sequences to the Model class - if it makes sense to do so.

----

I would second the refactoring suggestion.  However, at a grand total of 700 line of code, you are still a micro program that is easy to read and understand and maintain regardless of how it's built.  If you're not intending on growing this thing out beyond 3-5 thousand line you probably needn't bother.

----

Thanks for your advice. I have a few more questions, though. When you create a document-based application, does the MyDocument class become the Controller? My other question is, when you do have programs that are 3-5 thousand lines long, do you create multiple controller classes to do specific things with the interface to keep the code readable, or when your programs become that long will you have multiple windows that require a different controller for each?

----

I've always needed multiple windows each with their own controller class. And yeah, the NSDocument subclass is supossed to be a controller, and your model should be a different class, but right now I'm working on an app that is close the 10 thousand lines between all of the frameworks and bundles that it loads in, and the document class doubles for the model as well. It lends itself better that way (in my particular instance). Strictly, this is wrong, but if it flows and is manageable as well as understandable and easy to read, it is acceptable. Just don't let it get out of control. I actually added a compiler warning (using the #warning directive) to remind myself that it acts as both. Anyway. Hope this babble helped. --EliotSimcoe

----

Warning: I'm a Cocoa-Java developer, so I may do things a bit differently than Objective-C programmers...  that being said...

I will generally break up my window controller into several classes which each control different aspects of the window.  This is especially helpful when you have views like tables and drop-down boxes which also require a delegate / data souce.  That way, the sub-controller can present a greatly simplified view of that particular aspect of the interface.  To reduce unnecessary coupling, I have all the sub-controllers communicate only with their parent controller (i.e. the window controller) and any model objects they need.  If a particular sub-controller needs to communicate with another sub-controller, I have it call a method in the window controller to do so.  I've found that the following aspects make things work pretty well:


* Sub-controllers should be the target for the various UI elements and have  target methods named after the action they perform (e.g. "onOkClicked", "onNextClicked").
* The sub-controller should also have a seperate method to perform each action (e.g. "performOkClicked", "performNextClicked").
* The main controller should have a a perform method for each perform method in each of its sub-controllers.
* Sub-controllers have methods to permit access to data represented in their UI elements, and the main controller has matching methods which simply call those of the sub-controllers.


The main controller does not expose the fact that it has sub-controllers either to the sub-controllers themselves or any other part of the program.  It is important to maintain this encapsulation so that changes in the sub-controllers or their organization don't cause problems elsewhere in the application.

When an action is invoked (e.g. a button is clicked), the sub-controller will be notified of the event because it is the target for that button.  The sub-controller's target method will simply invoke the perform method on the main controller.  The main controller will invoke the perform method on the sub-controller which received the event, and then will proceed to call other methods on the other sub-controllers to keep them synchronized with the event (e.g. tell them to update the "enabled" property of their various controls).  The perform method of the sub-controller will contain the code to actually implement the desired action.

When a menu-item is selected which pertains to a specific window, then it will obtain the key window through the shared application, get its controller, and invoke the perform method of that window controller.  The window controller then delegates the implementation of that functionality to the appropriate sub-controller and synchronizes the other sub-controllers to the changes.

-- AndrewMiner

