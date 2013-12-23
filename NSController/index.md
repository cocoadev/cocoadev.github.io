---
layout: page
title: NSController
---



NSController is a new class introduced with Panther. It seems to be an InterfaceBuilder-integration component; you can create various "controller" objects in IB and form various bindings/glue that would previously have had to be done programmatically. Apparently, this is resurrected functionality from EnterpriseObjectsFramework. The controllers available in the BindingsPalette so far are NSObjectController, NSUserDefaultsController and NSArrayController. They look VERY useful, but mysterious - if anybody here has been to WWDC and knows more about them, please contribute!

----

NDA? What's an NDA?

Hey, *I* never signed one - everything up there is gleaned from the scraps of information on the net:) I was hoping that there would be someone with, say, EOF experience... --ThomasCastiglione

----

It's similar to the aspects stuff in EOF, but it's a new implementation.

----

In the vernacular: yeaaaaaah, baby!

----

So, does it do Object-relational mapping?    Does it tie into data sources, such as databases?  Or is it something completely different?  It sounds like its completely different.  

Sure would be nice if Apple would sell us EOF so we can use it in our cocoa apps (even in its Java form)

----

You can learn a bit more about the bindings by looking at the release note of XCode, and the FAQ as well.
I spent the night on that and have figured out how it works on single objects, but could not understand how it works for lists and table.
Appearently it is an extremely powerfull feature that should save so much coding. 

----

There's updated CurrencyConverter Tutorials available (one for NSObjectController, one for NSArrayController):
http://developer.apple.com/documentation/Cocoa/Conceptual/ControllerLayer/Tasks/ccwithbindings.html
http://developer.apple.com/documentation/Cocoa/Conceptual/ControllerLayer/Tasks/ccwithbindingsplus.html

----

Yep, it looks that way... but us developers who didn't shell out for WWDC are SOL... we cant' do squat until Panther is released and Apple finally goes public... since they've scared everyone into clamming up due to "NDA".... which is absurd as well because every apple competitor who might use the information sent someone to WWDC and has it anyway!  Who is this NDA protecting, really?   Its utter bullshit, and Apple needs to figure out a better way to deliniate what they want to protect.... API improvements are not trade secrets, for crying out loud.

NSController seems to be a bridge that plugs the M into the V of MVC.  EG, its a C of MVC that automagically has tables filled from arrays, etc. 

----

The NDA is there because Apple deems it necessary.  Not everything that's discussed at WWDC need be released with the final product.  When news is leaked, people's expectations are raised.  If Apple then "fails to deliver", that captures more mindshare than what was actually shipped.  Basic point: If you sign an NDA, you should stick by it.  If you don't like it, don't sign, and wait...

----

More info here:
http://developer.apple.com/documentation/Cocoa/Conceptual/ControllerLayer/index.html#//apple_ref/doc/uid/10000167i

