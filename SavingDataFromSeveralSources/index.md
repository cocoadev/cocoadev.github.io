---
layout: page
title: SavingDataFromSeveralSources
---

I'm starting with Cocoa and i'm getting mad trying to communicate between my classes.
I use controllers -as i learnt to- for most of sources. Here is my problem :

I have two custom NSControl subclass.

The first "plan" is a kind of list (big scrollable field) in my main window; wich contains instances of the second NSControl subclass "element".
The instances of "element" are created programatically by pressing a button "new element".

When a new "element" is created, it add itself as a subview of "plan". It also load a nib files into itself to show UI elements.

I want to save the whole plan data. I thought to use a NSMutableArray containing the "plan"'s elements, and a NSMutableDictionary to store each "element"'s data. So i would have a NSArray storing NSDictionary.

My problem is (i'm a newbie hmm hmm.) how can i read the content of the TexFields of the Nib inside each "elements"? If i create a NSArrayController in the Nib, and then bind it to my fields, how can i read this arrayController from the "element" source?

I'm not searching for a precise solution here as i know it's a little abstract.. I would like some help to structurate my code.

Where do i declare each arrays, dictionaries... how can the array of "plan"'s source can read and store the content of the dictionary of "element"... etc...

StephaneDassieu.

----

Your questions indicate you should start here: http://developer.apple.com/documentation/Cocoa/Conceptual/ObjCTutorial/index.html#//apple_ref/doc/uid/TP40000863

You will also need to read this: http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/index.html#//apple_ref/doc/uid/TP30001163

I say this because these are some pretty basic questions which require basic introductory knowledge that these documents were specifically created to answer. In addition, if I infer correctly, you intend to dynamically create an interface based on stored data. You need to conceptualize this as "I'm saving *a description* of this interface and recreating the interface based on the description", rather than approaching it as 'saving the interface itself'. The former is correct, the latter is a monstrosity and rightly difficult to do.

