---
layout: page
title: HavingTroubleLearningCocoa
---

*Editor's Note:* The prior content of this page has been refactored and added as discussion to the GoodBeginnerTutorial page

----

I've been working through Kochan's Objective-C book which is only concerned with console applications (at least at the point I'm at in it, which is not very far).  I've also got a 1st edition (! wish I had waited a month) of Hillegass's Cocoa book, and I'm wondering something that may be very simple, or may not be:

How does one import a class into InterfaceBuilder and/or how does one most easily get a new interface widget to talk to an old console class?

I'm getting a bit ahead of myself here, but I've programmed before in C (also console) and now that I've figured out just enough GUI goo to make a button (generated in IB) interact with an action (method for class created and instantiated in IB) I want to expand on that.  Unfortunately, with what I know in the GUI goo, I am unable to talk to any old text console classes.  I suspect that it will require somehow piping the values of the class variables and method returns out an outlet, and typing the method execution to "actions"... but...

Case in point, let's say you had the Calculator class finished (as of end of Chapter 4) and wanted to suddenly drop a GUI on top of the class and its very simple methods.  What does one do here?

chrism at lumin us

----

Add the ivars for your UI elements in your class' header file like

    
IBOutlet NSButton *myButton

 and add the actions like     -(IBAction)myAction:(id)sender

Then just drag the header file onto IB's main window. 

The IBOutlet and IBAction let IB know what things in the file are meant for it.

In the action method, you would typically grab the values for your other UI elements, and pass those on to another method. The     sender parameter to the action will be a pointer to the button (or whatever) which called the method.

----

I have all three sections (interface, implementation, main) in just one file in the console programs.  It would probably be easier just to rewrite the methods especially now that I consider that running something like a calculator in an event driven environment will take some backwards thinking.  No wonder so many people make RPN calculators ;) ...  IB does not like me dragging my old main.m onto it's class window :/

----

*Actually it's the :(id)sender that tells IB that the action could be used in IB, thus I usually use - (void)foo:(id)sender instead as it is less to type, not to mention IBAction is #defined as void.*

**Double actually:**

Interface Builder recognizes methods as actions if they have a     void return type and a single parameter named     sender. The type of the     sender parameter can be     id, or another class such as     NSButton. Interface Builder looks for the     sender parameter name as a keyword when parsing methods for actions. If you want more meaningful argument names you can explicitly state that a method is an action using the     IBAction return type. Interface Builder would recognize the following lines as action methods:
    
- (void)anAction:(id)sender;
- (void)aButtonAction:(NSButton *)sender;
- (IBAction)aButtonAction:(NSButton *)aButton;

-- BookCocoaInANutshell

----

OK - just finished BookLearningCocoaObjCSecond, and feel *fairly* comfortable with what I have read but I still feel I'm missing a huge chunk of knowledge about the whole Cocoa game.

So  - where do I go / what do I do next? Should I read more books? I'm very tempted by BookCocoaProgMacOSX but with the news of a 2nd edition coming out in April I'm going to hold fire. Does the "huge chunk of knowledge" only come with practise?

----

Well do all the example and start programming! Reading will only get you so far...but now you have to learn to do it from memory, etc. So start programming away! Make a whole bunch of mini apps, like text editors, calculators, etc..

----

The first thing I would do is head over to [http://developer.apple.com/documentation/Cocoa/Cocoa.html] and spend some time reading Apple's documentation. I think learning Cocoa 'from memory' is a waste of time when everything is right there. Just familiarize yourself with the classes and what they're for. You can look up specific methods as you need them. Then pick a project and get to work! Try to make it something that's actually going to be useful for you, rather than a typical garbage newbie project you'll never open again. Write an app to clean off your desktop, for example, sorting and archiving files by type, date, whatever. You might get some ideas from NewbieProjectIdeas

----

A few months ago I had finished most of "Learning Cocoa with Objective-C" 2nd Edition and felt pretty lost. I mean I understood everything they covered and I could modify the code examples a bit but I couldn't really understand errors that I would generate when trying to write my own applications. So I bought and worked through "Programming in Objective-C" by Kochan and it dove-tailed perfectly for me. It starts with Objective-C and then covers the Foundation framework while "Learning Cocoa with Objective-C" focuses on the Application framework. It's helped me move forward a lot by giving me an understanding of the more basic parts of Objective-C. Now I'm using "Cocoa Recipes for MacOS X" and reading and understanding the developer documentation which I couldn't do after reading "Learning Cocoa with Objective-C" but before reading "Programming in Objective-C".

----

You sound like you are ahead of where I was since I come from a strong c background with almost no OOP experience and no recent programming experience at all.

