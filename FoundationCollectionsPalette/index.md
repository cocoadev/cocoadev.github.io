---
layout: page
title: FoundationCollectionsPalette
---

The FoundationCollectionsPalette extends Apple's InterfaceBuilder with support for Foundation's collection classes; it enhances NSDictionary and NSArray, adding an attributes inspector, a connections inspector and a size inspector to both.

Using all this is as easy as building this project and loading the resulting palette in IB; the inspectors themselves are as obvious as I could make them. The code itself is intended to be a rough template for creating useful palettes in IB. Feel free to use this project as you wish.

Go to http://www.geocities.com/kritter_cocoadev for the code. This is page should be used for support.

Please add any comments, suggestions, bug-reports, etc. under this header::

----**Comments, suggestions, bug-reports, etc.**----

Excellent demonstration of IB "internals". One thing I miss is how I can dynamically implement target/action, that is, how can one extend the connection inspector of NSView's, eg NSButton. If I connect the target of a button view a custom object it shows it's Objective-C methods, but how can I (dynamically) add methods to this list, more specifically, for a proxy which forwards actions using -forwardInvocation: ? Eg this proxy has reflection methods like -(NSArray *)forwardedSelectorNames; which I would like to show up as actions. -- ?

Can you not just add actions to the proxy class in IB? -- KritTer

There are (at least) two issues with this:
a) the actions would appear on any proxy instance, which is wrong because each proxy wraps a different source object and therefore has different actions
b) this would be annoying, the proxy already knows what actions it provides, so why do I need to specifiy them again :-(

The former, yes, but how would a proxy know for sure what it will refer to during the entire course of program execution? It might change. Alas, I cannot help you with a palette, as actions are class-based, not object, and Apple is keeping the interface obscure...

